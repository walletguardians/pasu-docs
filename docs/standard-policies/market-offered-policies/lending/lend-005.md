---
description: Repaying Another Address's Debt (WARN)
---

# LEND-005: 내가 아닌 다른 주소의 빚을 상환할 시 경고

### Policy Definition (정책 정의)

> 상환하는 빚이 **내 주소가 아닌 다른 주소**의 빚인 경우, 내 자금으로 남의 빚을 갚는 게 맞는지 확인하도록 경고합니다.

Aave나 Morpho 같은 대출 프로토콜의 상환(repay)에는 `onBehalfOf`라는 칸이 있어, 내 지갑에서 돈을 내되 빚을 줄여줄 대상은 다른 주소로 지정할 수 있습니다. 이 칸을 다른 주소로 채워두면, 내 돈으로 모르는 사람의 대출만 갚아주고 내 포지션은 그대로 남는 일이 벌어집니다. 상환 대상 주소가 서명하는 본인과 다르면, 정말 남의 빚을 갚으려는 게 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜의 상환 서명. 빚을 갚아줄 대상(`onBehalfOf`)이 서명하는 본인 주소와 다른 경우에 적용됩니다. (Repay)

#### Audience (대상 사용자)

Aave·Morpho 등에서 대출을 쓰며, 평소 자기 포지션만 상환하는 사용자

#### Used Data (판정에 사용될 데이터)

* 빚을 갚아줄 대상 주소
* 이번 거래를 서명하는 본인 주소

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("repay-on-behalf-confirm-warn")
@severity("warn")
@reason("이 상환은 본인이 아닌 다른 주소의 빚을 갚습니다 — 내 자금으로 남의 대출을 갚는 게 맞는지 확인하세요")
forbid(principal, action == Lending::Action::"Repay", resource)
when {
  context has onBehalfOf
  && context.onBehalfOf != context.meta.submitter
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "repay-on-behalf-confirm-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "repay" } } }
}
```
{% endcode %}

***

**LEND-005: 내가 아닌 다른 주소의 빚을 상환할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
