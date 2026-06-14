---
description: HyperLiquid Order on a Denylisted Market (DENY)
---

# PERP-021: 차단 목록에 있는 마켓에 주문을 넣을 시 차단

### Policy Definition (정책 정의)

> HyperLiquid에서 **차단 목록에 올린 마켓**으로 포지션을 새로 잡거나 키우는 주문이면, 서명 전에 막습니다.

HyperLiquid는 마켓마다 심볼이 따로 있습니다(예: `DOGE`, `kPEPE`). 변동성이 너무 크거나 손대지 않기로 정한 마켓은 차단 목록에 올려두고, 그 마켓으로 들어가는 주문을 아예 막을 수 있습니다. 포지션을 줄이는 reduce-only 주문은 손실을 정리하는 쪽이라 건드리지 않고, 차단한 마켓에 새로 진입하거나 물량을 늘리는 주문만 막습니다. 주문의 마켓 심볼이 차단 목록에 들어 있다면, 주문이 나가기 전에 차단합니다.

#### Scope (적용 범위)

HyperLiquid 주문(PlaceOrder). 주문의 마켓 심볼이 블랙리스트(`DOGE`, `kPEPE`, `kSHIB`)에 있고, 포지션을 줄이는 reduce-only 주문이 아닐 때 적용됩니다. (Place Order)

#### Audience (대상 사용자)

특정 마켓은 손대지 않기로 정해두고 HyperLiquid에서 perp을 거래하는 사용자

#### Used Data (판정에 사용될 데이터)

주문의 마켓 심볼 (블랙리스트)

포지션을 줄이는 주문인지 여부

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-symbol-denylisted-deny")
@severity("deny")
@reason("이 Hyperliquid 마켓이 차단 목록에 있어 정책에 따라 주문을 차단했습니다")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    ["DOGE", "kPEPE", "kSHIB"].contains(context.market.symbol)
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-symbol-denylisted-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "marketDenylist": {
      "cedar_set": "[\"DOGE\", \"kPEPE\", \"kSHIB\"]",
      "default": "DOGE, kPEPE, kSHIB",
      "meaning": "Block any order whose market symbol IS in this set. Use HL UNIVERSE names, matched exactly (case-sensitive): HL lists 1000-unit memecoin perps with a k prefix (kPEPE, kSHIB, kBONK — NOT PEPE/SHIB). Placeholder examples — replace with the markets you want banned."
    }
  }
}
```
{% endcode %}

***

**PERP-021: 차단 목록에 있는 마켓에 주문을 넣을 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
