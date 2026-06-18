---
description: Unspecified Output Amount in Exact-Input Swaps (WARN)
---

# AMM-005: 판매할 토큰의 수량을 정하는 Swap(Exact-Input)에서, 수령할 토큰의 수량을 정하지 않았을 시 경고

### Policy Definition (정책 정의)

> 사용자가 판매할 토큰의 수량을 정하는 방식의 Swap을 시도할 때, 수령할 토큰의 최소 수량을 정하지 않은 경우, 그 사실을 파악하고 수정할 수 있게 경고를 표시합니다.

수령할 토큰의 최소 수량이 0으로 설정되어 있으면, MEV의 일종인 샌드위치 공격, 또는 선행 매매에 의해 시세보다 매우 낮은 금액으로 거래를 체결할 수 있습니다. 따라서 안전하게 Swap을 진행할 수 있도록 수령할 토큰의 최소 수량을 정하지 않은 경우 값을 반영할 수 있도록 경고를 표시합니다.

#### Scope (적용 범위)

Exact-Input 타입의 Swap을 수행할 때 적용됩니다.

#### Audience (대상 사용자)

DEX를 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터) &#x20;

* Swap의 종류
* 토큰의 최소 수량

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("swap-min-out-zero")
@severity("warn")
@reason("이 Swap으로 받을 토큰의 최소 수량이 지정되어 있지 않습니다. MEV를 당하지 않게 값을 지정하세요.")
forbid(principal, action == Amm::Action::"Swap", resource)
when {
  context.direction.kind == "exact_input"
  && context.direction has minAmountOut
  && context.direction.minAmountOut == "0x0"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "swap-min-out-zero",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "swap"
      }
    }
  }
}

```
{% endcode %}

***

**AMM-005: 판매할 토큰의 수량을 정하는 Swap(Exact-Input)에서, 수령할 토큰의 수량을 정하지 않았을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/10\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
