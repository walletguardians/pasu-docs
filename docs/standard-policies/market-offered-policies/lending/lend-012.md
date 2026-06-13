---
description: Collateral Withdrawal That Drops Health Factor Below 1.5 (WARN)
---

# LEND-012: 담보를 인출해 건강도가 1.5 미만이 될 시 경고

### Policy Definition (정책 정의)

> 이번 담보 인출로 **건강도(health factor)가 1.5 아래로 떨어지는** 경우, 청산에 가까워졌다고 보고 경고합니다.

대출 프로토콜에서 빌린 돈이 있는 상태로 담보를 빼면, 담보 대비 빚의 비율이 올라가 건강도가 낮아집니다. 건강도가 1 밑으로 내려가면 누구든 내 담보를 청산해 헐값에 가져갈 수 있습니다. 1.5는 그 청산선 바로 위의 완충 구간으로, 여기까지 내려오면 시세가 조금만 흔들려도 청산권에 들어섭니다. 그래서 인출 뒤 예상 건강도가 1.5 미만이면, 정말 이만큼 빼도 되는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Aave·Morpho 등 대출 프로토콜에서 담보를 빼내는 인출에 적용됩니다. 인출하고 남는 포지션의 예상 건강도가 1.5 미만일 때 발동합니다. (Withdraw)

#### Audience (대상 사용자)

대출 프로토콜에서 담보를 맡기고 돈을 빌려 쓰는 사용자

#### Used Data (판정에 사용될 데이터)

이번 인출 수량을 반영해 다시 계산한 인출 후 예상 건강도 (`postActionHf` — 서버 조회 결과). 계산에는 인출하려는 프로토콜(`venue`), 담보 자산(`asset`), 인출 수량(`amount`)을 넘깁니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdraw-low-health-factor-warn")
@severity("warn")
@reason("이만큼 담보를 빼면 건강도(health factor)가 1.5 미만으로 청산에 가까워집니다 — 확인하세요")
forbid(principal, action == Lending::Action::"Withdraw", resource)
when {
  context has custom
  && context.custom has postActionHf
  && context.custom.postActionHf.lessThan(decimal("1.5000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdraw-low-health-factor-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "withdraw" } } },
  "policy_rpc": [{
    "id": "post-withdraw-hf",
    "method": "lending.health_factor",
    "params": {
      "chain_id": "$.root.chain_id",
      "owner": "$.root.from",
      "venue": "$.action.venue",
      "asset": "$.action.asset",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "postActionHf", "type": "Decimal", "from": "$.result.postActionHf", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "postActionHf": "decimal" } }
}
```
{% endcode %}

***

**LEND-012: 담보를 인출해 건강도가 1.5 미만이 될 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
