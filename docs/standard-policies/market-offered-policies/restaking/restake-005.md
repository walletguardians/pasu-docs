---
description: Restaking Deposit Above USD Cap (WARN)
---

# 리스테이크 예치 금액이 $50,000을 초과할 시 경고

### Policy Definition (정책 정의)

> EigenLayer에 한 번에 예치하는 리스테이크 수량이 미리 정해둔 한도를 넘으면, 규모를 다시 확인하도록 경고합니다.

EigenLayer에 리스테이킹된 자산은 슬래싱 대상이 될 수도 있고, 빼낼 때도 시간이 걸립니다.

이 정책은 Eigenlayer에 거액을 예치하는 경우, 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

EigenLayer에 토큰을 예치해 리스테이크 지분으로 묶는 경우에 적용됩니다.

#### Audience (대상 사용자)

Eigenlayer 리스테이킹 사용자

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
\&#xNAN;_Supported Chain: Ethereum_
