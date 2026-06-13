---
description: Restaking Deposit Above USD Hard Cap (DENY)
---

# RESTAKE-006: 리스테이크 예치 금액이 $250,000을 초과할 시 경고

### Policy Definition (정책 정의)

> 한 번에 EigenLayer에 예치하는 리스테이크 금액이 미화 $250,000을 넘으면, 예치가 나가지 않도록 경고합니다.

EigenLayer 리스테이크 예치는 내 자산을 스트래티지(strategy)에 넣어 오퍼레이터에게 위임할 지분으로 묶는 일입니다. 예치한 자산은 슬래싱 대상이 되고, 빼려면 출금 큐에 넣고 대기 기간을 거쳐야 해서 한 번에 큰 금액을 넣을수록 묶이는 자산과 손실 위험도 그만큼 커집니다. 그래서 한 번 예치하는 금액을 시세로 환산한 값이 $250,000이라는 강제 상한을 넘으면, 경고에 그치지 않고 예치 자체를 막습니다.

#### Scope (적용 범위)

EigenLayer 리스테이크 예치에 적용됩니다. 예치하는 자산과 수량을 시세로 환산했을 때 $250,000을 넘는 경우에만 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에 한 번에 큰 금액을 리스테이크하면서 예치 규모에 상한을 두려는 사용자

#### Used Data (판정에 사용될 데이터)

예치하는 자산과 수량의 USD 환산 가치 (`restakeUsd` — 시세 조회 결과). 환산에는 예치 자산의 주소(`action.token.key.address`)와 수량(`action.amount`)을 사용합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-usd-hardcap-deny")
@severity("warn")
@reason("리스테이크 예치가 설정한 USD 강제 상한을 초과합니다.")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when
{
    context has custom &&
    context.custom has restakeUsd 
    &&
    context.custom.restakeUsd.greaterThan(decimal("250000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-usd-hardcap-deny",
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
    "outputs": [{ "kind": "context", "field": "restakeUsd", "type": "Decimal", "from": "$.result.usd", "required": true }],
    "optional": false
  }],
  "custom_context": { "fields": { "restakeUsd": "decimal" } }
}
```
{% endcode %}

***

**RESTAKE-006: 리스테이크 예치 금액이 $250,000을 초과할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
