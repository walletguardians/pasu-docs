---
description: Disabling Collateral Pushes Health Factor Near Liquidation (WARN)
---

# LEND-013: 담보를 해제하면 청산에 가까워질 시 경고

### Policy Definition (정책 정의)

> 담보로 잡힌 자산을 해제했을 때 **건강도(health factor)**가 청산 문턱에 가까운 1.1 아래로 떨어지는 경우, 서명 전에 경고합니다.

대출 프로토콜에서 예치한 자산은 담보로 잡혀, 그 가치만큼 다른 토큰을 빌릴 수 있게 해줍니다. 빌린 돈이 남아 있는데 담보 하나를 빼버리면, 빌릴 수 있던 한도가 줄어 건강도가 내려갑니다. 건강도는 담보가 빚을 얼마나 받치고 있는지를 나타내는 값으로, 1 밑으로 내려가면 청산이 시작돼 담보를 헐값에 잃습니다. 이 담보를 해제한 뒤의 건강도가 1.1 미만으로 청산 문턱에 바싹 붙으면, 정말 빼도 되는지 다시 보도록 경고합니다.

#### Scope (적용 범위)

Aave·Morpho 등 대출 프로토콜에서 담보를 해제하는 동작에 적용됩니다. 해제 후 예상 건강도가 1.1 미만일 때만 발동합니다. (Disable Collateral)

#### Audience (대상 사용자)

빚을 낸 채로 담보 구성을 자주 바꾸는 대출 이용자

#### Used Data (판정에 사용될 데이터)

이 담보를 해제한 뒤로 계산한 건강도 (`postActionHf` — 지갑 주소·해제 자산·수량을 넘겨 받은 서버 조회 결과)

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
&#xNAN;_&#x53;upported Chain: Ethereum_
