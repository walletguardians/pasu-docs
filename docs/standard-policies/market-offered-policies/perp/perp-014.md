---
description: Adding Risk to a Position Near Its Liquidation Price (WARN)
---

# PERP-014: 청산가까지 10% 이내로 가까운 포지션에 리스크를 더할 시 경고

### Policy Definition (정책 정의)

> 이미 청산가까지 10% 이내로 몰린 Hyperliquid 포지션에 리스크를 더하는 주문이면, 청산 위험을 다시 확인하도록 경고합니다.

Hyperliquid 무기한 선물에서는 시세가 청산가에 닿으면 포지션이 강제로 정리되고 증거금을 잃습니다. 청산가까지 남은 거리가 좁을수록 시세가 조금만 불리하게 움직여도 청산으로 넘어갑니다. 이런 상황에서 포지션을 줄이지 않고 오히려 같은 방향으로 더 키웠을 때 방향이 틀렸다면 손실은 그만큼 불어납니다. 그래서 청산가까지 남은 거리가 10% 미만인 포지션에 리스크를 더하는 주문이면, 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에 내는 주문 중, 포지션을 줄이는 리듀스온리(reduce-only) 주문이 아니면서 해당 마켓의 기존 포지션이 청산가까지 10% 이내로 가까운 경우에 적용됩니다.&#x20;

#### Audience (대상 사용자)

Hyperliquid에서 청산가 근처까지 포지션을 끌고 가는 트레이더

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 기존 포지션의 청산가까지 남은 거리

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-liquidation-proximity-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓의 포지션이 청산가까지 10% 미만입니다 — 여기서 리스크를 더하면 청산될 수 있으니 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has liquidationDistanceBps &&
    context.liquidationDistanceBps < 1000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-liquidation-proximity-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "minLiqDistanceBps": {
      "cedar_long": "1000",
      "default": "1000 bps (10%)",
      "meaning": "Warn when the existing position's liquidation price is closer than this (|markPx − liqPx| / markPx, bps) and the order adds risk. Placeholder — tune to your comfort buffer."
    }
  }
}
```
{% endcode %}

***

**PERP-014: 청산가까지 10% 이내로 가까운 포지션에 리스크를 더할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
