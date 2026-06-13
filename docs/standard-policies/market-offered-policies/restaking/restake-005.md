---
description: Restaking Deposit Above USD Cap (WARN)
---

# RESTAKE-005: 리스테이크 예치 금액이 $50,000을 초과할 시 경고

### Policy Definition (정책 정의)

> EigenLayer에 리스테이크로 예치하는 금액이 미화 약 $50,000을 넘는 경우, 규모를 다시 확인하도록 경고합니다.

EigenLayer 리스테이크 예치는 보유한 스테이킹 자산을 스트래티지에 넣어, 나중에 오퍼레이터에게 위임할 지분으로 묶어두는 일입니다. 한 번 넣은 자산은 오퍼레이터에게 위임되면 슬래싱 위험에 노출되고, 빼려면 출금 큐를 거쳐 대기 기간을 기다려야 해서 곧바로 회수하기 어렵습니다. 그래서 한 번에 넣는 금액이 클수록 묶이는 자산과 슬래싱에 노출되는 규모도 같이 커집니다. 예치하는 자산을 시세로 환산한 값이 기준을 넘으면, 이만큼 묶어둘 생각이 맞는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

EigenLayer 스트래티지에 리스테이크로 자산을 예치하는 경우에 적용됩니다. 예치하는 자산을 시세로 환산했을 때 $50,000을 넘는 경우에 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에 큰 금액을 리스테이크하려는 사용자

#### Used Data (판정에 사용될 데이터)

예치하는 토큰(`action.token.key.address`)과 수량(`action.amount`)을 USD로 환산한 가치 (`restakeUsd` — 시세 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("restake-usd-cap-warn")
@severity("warn")
@reason("고액 리스테이크 예치입니다(약 $50,000 초과) — 예치 규모를 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when
{
    context has custom &&
    context.custom has restakeUsd &&
    context.custom.restakeUsd.greaterThan(decimal("50000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "restake-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } },
  "policy_rpc": [{
    "id": "restake-usd",
    "method": "oracle.usd_value",
    "params": {
      "chain_id": "$.root.chain_id",
      "asset": "$.action.token.key.address",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "restakeUsd", "type": "Decimal", "from": "$.result.usd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "restakeUsd": "decimal" } }
}
```
{% endcode %}

***

**RESTAKE-005: 리스테이크 예치 금액이 $50,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
