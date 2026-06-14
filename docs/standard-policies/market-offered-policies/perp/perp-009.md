---
description: Order on a Low-Leverage-Tier (Illiquid) HyperLiquid Market (WARN)
---

# PERP-009: 레버리지 등급이 낮은 마켓에 주문할 시 경고

### Policy Definition (정책 정의)

> HyperLiquid에서 최대 레버리지 등급이 낮은(10배 이하) 마켓에 새로 진입하거나 물량을 더 얹는 주문이면, 호가가 얇은 마켓인지 다시 확인하도록 경고합니다.

HyperLiquid은 마켓마다 허용하는 최대 레버리지 등급을 다르게 매깁니다. 호가가 두껍고 거래가 활발한 메이저 마켓은 높은 레버리지를 열어주지만, 호가가 얇아 시세가 쉽게 출렁이는 마켓은 최대 레버리지를 낮게 묶어둡니다. 이런 마켓에서는 진입가와 청산가가 조금만 벌어져도 슬리피지로 손해를 보거나, 누군가 호가를 흔들어 청산을 유도하기 쉽습니다. 그래서 마켓의 최대 레버리지 등급이 10배 이하인데 포지션을 새로 여는 주문이면, 이 마켓에 들어가도 괜찮은지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid에 내는 주문(PlaceOrder) 중, 마켓의 최대 레버리지 등급이 10배 이하인 경우에 적용됩니다. 포지션을 줄이는 reduce-only 주문에는 발동하지 않고, 새로 열거나 물량을 더하는 주문에만 적용됩니다. (Place Order)

#### Audience (대상 사용자)

HyperLiquid에서 메이저 마켓뿐 아니라 신생 토큰이나 거래량 적은 마켓의 perp까지 거래하는 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* HyperLiquid 최대 레버리지 등급

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-illiquid-market-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓은 최대 레버리지 등급이 낮습니다(10배 이하) — 호가가 얇고 조작되기 쉽다는 HL의 신호이니 열거나 추가하기 전에 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has maxLeverage &&
    context.maxLeverage <= 10
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-illiquid-market-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "maxLeverageTierFloor": {
      "cedar_long": "10",
      "default": "10x tier",
      "meaning": "Warn when the market's HL max-leverage tier is at or below this (low tier = HL's own illiquidity signal). Placeholder — raise to 20 to also flag mid-caps, lower to 5 for only the thinnest books."
    }
  }
}
```
{% endcode %}

***

**PERP-009: 레버리지 등급이 낮은 마켓에 주문할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
