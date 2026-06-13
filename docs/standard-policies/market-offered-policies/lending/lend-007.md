---
description: Lending Supply Above USD Cap (WARN)
---

# LEND-007: 예치 금액이 $50,000을 초과할 시 경고

### Policy Definition (정책 정의)

> 한 번에 예치하는 금액이 미화 약 $50,000을 넘는 경우, 규모를 다시 확인하도록 경고합니다.

Aave나 Morpho 같은 대출 프로토콜에 토큰을 예치하면 그만큼 담보가 쌓이고, 이를 바탕으로 대출을 받을 수 있습니다. 다만 예치 한 번에 큰 금액을 넣는다는 건, 잘못 누른 풀이거나 들어가려던 마켓이 아닐 때 한 번에 묶이는 돈도 그만큼 크다는 뜻입니다. 예치 금액을 시세로 환산한 값이 기준을 넘으면, 풀과 금액이 맞는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜에 토큰을 예치하는 서명. 예치하는 토큰과 수량을 시세로 환산했을 때 $50,000을 넘는 경우에 적용됩니다. (Supply)

#### Audience (대상 사용자)

대출 프로토콜에 한 번에 큰 금액을 예치하는 사용자

#### Used Data (판정에 사용될 데이터)

예치하는 토큰과 수량의 USD 환산 가치 (`supplyUsd` — 시세 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("supply-usd-cap-warn")
@severity("warn")
@reason("고액 예치입니다(약 $50,000 초과) — 예치 규모를 확인하세요")
forbid(principal, action == Lending::Action::"Supply", resource)
when {
  context has custom
  && context.custom has supplyUsd
  && context.custom.supplyUsd.greaterThan(decimal("50000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "supply-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "supply" } } },
  "policy_rpc": [{
    "id": "supply-usd",
    "method": "oracle.usd_value",
    "params": {
      "chain_id": "$.root.chain_id",
      "asset": "$.action.asset",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "supplyUsd", "type": "Decimal", "from": "$.result.usd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "supplyUsd": "decimal" } }
}
```
{% endcode %}

***

**LEND-007: 예치 금액이 $50,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
