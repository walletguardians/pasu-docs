---
description: Borrow That Pushes Health Factor Below 1.5 (WARN)
---

# LEND-011: 대출 후 Health Factor가 1.5 미만이 될 시 경고

### Policy Definition (정책 정의)

> 이번 **대출(Borrow)** 이 실행되면 Health Factor가 1.5 미만으로 내려가는 경우, 청산 위험을 다시 확인하도록 경고합니다.

대출 프로토콜은 Health Factor로 위험도를 보여줍니다. 이 값이 1까지 떨어지면 포지션이 청산되어 담보를 잃습니다. 더 빌릴수록 HF는 낮아지고, 1에 가까울수록 시세가 조금만 흔들려도 청산으로 넘어갑니다. 그래서 해당 대출을 반영한 건강도를 계산해, 그 값이 1.5 미만이면 빌리는 금액이 적절한지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜에서 자산을 빌리는 트랜잭션. 이번 대출을 반영한 HF가 1.5 미만으로 떨어지는 경우에 적용됩니다.&#x20;

#### Audience (대상 사용자)

대출 프로토콜에서 담보를 맡기고 돈을 빌려 쓰는 사용자

#### Used Data (판정에 사용될 데이터)

* 대출 실행 후의 HF (빌리는 자산과 금액을 반영해 계산한 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("borrow-low-health-factor-warn")
@severity("warn")
@reason("이 대출 후 건강도(health factor)가 1.5 미만으로 청산에 가까워집니다 — 확인하세요")
forbid(principal, action == Lending::Action::"Borrow", resource)
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
  "id": "borrow-low-health-factor-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "borrow" } } },
  "policy_rpc": [{
    "id": "post-borrow-hf",
    "method": "lending.health_factor",
    "params": {
      "chain_id": "$.root.chain_id",
      "owner": "$.root.from",
      "venue": "$.action.venue",
      "asset": "$.action.asset",
      "amount": "$.action.amount",
      "action_kind": "borrow"
    },
    "outputs": [{ "kind": "context", "field": "postActionHf", "type": "Decimal", "from": "$.result.postActionHf", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "postActionHf": "decimal" } }
}
```
{% endcode %}

***

**LEND-011: 대출 후 건강도(health factor)가 1.5 미만으로 떨어질 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
