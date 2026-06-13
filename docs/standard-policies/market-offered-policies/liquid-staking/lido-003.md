---
description: Large stETH-to-wstETH Wrap Above the Size Limit (DENY)
---

# LIDO-003: 대량의 stETH를 wstETH로 래핑할 시 차단

### Policy Definition (정책 정의)

> 한 번에 정해진 한도를 넘는 **대량의 stETH**를 wstETH로 래핑하면 **차단**합니다.

래핑(Wrap)은 리베이스로 잔액이 변하는 stETH를 잔액이 고정된 wstETH로 바꾸는 작업입니다. 한 번에 너무 큰 금액을 래핑하면, 단순 실수로 자릿수를 잘못 입력했거나 악성 dApp이 보유량 대부분을 한꺼번에 묶으려는 거래일 수 있습니다. 그래서 이 정책은 한 번의 래핑 금액이 미리 정한 한도(기본 100 stETH)를 넘으면 서명을 막습니다.

#### Scope (적용 범위)

Lido에서 stETH를 wstETH로 래핑하는 거래에 적용됩니다. 래핑 금액이 한도(기본 100 stETH)를 넘을 때 차단합니다. (Wrap)

#### Audience (대상 사용자)

Lido에 stETH를 예치해 두고 wstETH로 자주 래핑하는 사용자

#### Used Data (판정에 사용될 데이터)

거래가 일어나는 곳이 Lido인지(`context.venue.name`)와 이번 래핑 금액(`context.amountNano`). 금액은 토큰 개수 기준으로 환산해 한도(`100000000000` = 100 stETH)와 비교합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("wrap-amount-guard-deny")
@severity("deny")
@reason("한 번에 많은 양의 stETH를 wstETH로 래핑(wrap)하는 대량 거래입니다 — 차단했습니다")
forbid (
    principal,
    action == LiquidStaking::Action::"Wrap",
    resource
)
when
{
    context.venue.name == "lido" &&
    context has amountNano &&
    context.amountNano >= 100000000000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "wrap-amount-guard-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "wrap" }
    }
  },
  "_template": {
    "maxWrapNano": { "cedar_literal": "100000000000", "default": "100 stETH (1 stETH = 1e9 amountNano)", "meaning": "deny when a single wrap's amountNano >= this" }
  }
}
```
{% endcode %}

***

**LIDO-003: 대량의 stETH를 wstETH로 래핑할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
