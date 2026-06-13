---
description: Bulk Unwrap of wstETH into stETH (DENY)
---

# LIDO-004: 대량의 wstETH를 stETH로 언래핑할 시 경고

### Policy Definition (정책 정의)

> 한 번에 **대량의 wstETH**를 stETH로 언래핑(Unwrap)하려는 경우 경고합니다.

언래핑(Unwrap)은 가격이 고정된 wstETH를 리베이스되는 stETH로 되돌리는 작업입니다. 평소 쓰는 양을 훌쩍 넘는 wstETH를 한 번에 stETH로 푸는 거래는 본인이 직접 큰 포지션을 정리하는 경우도 있지만, 지갑이 털려 보유 물량을 한꺼번에 빼내려는 신호일 때도 있습니다. 그래서 한 번의 언래핑 양이 정해 둔 한도를 넘으면 거래를 경고합니다.

#### Scope (적용 범위)

Lido에서 wstETH를 stETH로 푸는 언래핑에 적용됩니다. 한 번의 언래핑 양이 100 wstETH 이상일 때 발동합니다. (Unwrap)

#### Audience (대상 사용자)

wstETH를 보유하며 큰 단위로 거래하는 Lido 스테이커

#### Used Data (판정에 사용될 데이터)

거래가 일어나는 프로토콜(`context.venue.name`)과 이번에 언래핑하는 wstETH 양(`context.amountNano`). `amountNano`는 wstETH 1개를 10억(1e9)으로 환산한 값이라, 한도 100억(1e11)은 100 wstETH에 해당합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("unwrap-amount-guard-deny")
@severity("deny")
@reason("한 번에 많은 양의 wstETH를 stETH로 언래핑(unwrap)하는 대량 거래입니다")
forbid (
    principal,
    action == LiquidStaking::Action::"Unwrap",
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
  "id": "unwrap-amount-guard-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "unwrap" }
    }
  },
  "_template": {
    "maxUnwrapNano": { "cedar_literal": "100000000000", "default": "100 wstETH (1 wstETH = 1e9 amountNano)", "meaning": "deny when a single unwrap's amountNano >= this" }
  }
}
```
{% endcode %}

***

**LIDO-004: 대량의 wstETH를 stETH로 언래핑할 시 경고**

Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
