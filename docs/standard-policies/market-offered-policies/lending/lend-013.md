---
description: Disabling Collateral Pushes Health Factor Near Liquidation (WARN)
---

# LEND-013: 담보 해제 후 Health Factor가 1.1 미만일 시 경고

### Policy Definition (정책 정의)

> 담보로 잡힌 자산을 해제했을 때 Health Factor가 1.1 아래로 떨어지는 경우, 서명 전에 경고합니다.

대출 프로토콜에서 예치한 자산은 담보로 잡혀, 그 가치만큼 다른 토큰을 빌릴 수 있게 해줍니다. 빌린 돈이 남아 있는데 담보 하나를 빼버리면, 빌릴 수 있던 한도가 줄어 HF가 내려갑니다. 해당 담보 해제를 반영한 건강도를 계산해, 그 값이 1.1 미만이면 의도된 행동인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Aave·Morpho 등 대출 프로토콜에서 담보 수량을 줄이는 동작에 적용됩니다. 감소 후 예상 건강도가 1.1 미만일 때만 발동합니다.

#### Audience (대상 사용자)

빚을 낸 채로 담보 구성을 자주 바꾸는 대출 이용자

대출 프로토콜에서 담보 수량을 줄이려는 사용자

#### Used Data (판정에 사용될 데이터)

* 담보 해제 후의 HF(감소될 담보와 금액을 반영해 계산한 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("disable-collateral-while-borrowing-warn")
@severity("warn")
@reason("이 담보를 해제하면 건강도(health factor)가 1.1 미만으로 청산에 가까워집니다 — 확인하세요")
forbid(principal, action == Lending::Action::"DisableCollateral", resource)
when {
  context has custom
  && context.custom has postActionHf
  && context.custom.postActionHf.lessThan(decimal("1.1000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "disable-collateral-while-borrowing-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "disable_collateral" } } },
  "policy_rpc": [{
    "id": "post-disable-hf",
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

**LEND-013: 담보를 해제하면 청산에 가까워질 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
