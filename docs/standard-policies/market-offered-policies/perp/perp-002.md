---
description: Placing a Perp Order Above 10x Leverage (WARN)
---

# PERP-002: 레버리지가 10배를 초과하는 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> 이 **Hyperliquid 마켓**의 계정 레버리지가 10배를 넘는 상태에서 포지션을 열거나 늘리는 주문이면, 청산 위험을 다시 확인하도록 경고합니다.

Hyperliquid 같은 perp 거래소는 적은 증거금으로 큰 포지션을 잡을 수 있게 레버리지를 허용합니다. 레버리지가 높을수록 시세가 조금만 반대로 움직여도 청산가에 금방 닿고, 손실도 그만큼 빠르게 불어납니다. 10배 레버리지면 가격이 10%만 거슬러도 증거금이 거의 다 날아가는 셈입니다. 그래서 지금 마켓의 레버리지가 10배를 넘는데 포지션을 새로 열거나 더 키우는 주문이면, 이 정도 레버리지가 맞는지 서명 전에 한 번 더 따져보도록 경고합니다. 포지션을 줄이는 reduce-only 주문은 위험을 키우지 않으므로 건드리지 않습니다.

#### Scope (적용 범위)

Hyperliquid의 perp 주문. 마켓 레버리지가 10배를 넘고, 포지션을 줄이는 reduce-only 주문이 아닐 때 적용됩니다. (Place Order)

#### Audience (대상 사용자)

Hyperliquid에서 perp을 거래하며 고배율 레버리지로 포지션을 잡는 사용자

#### Used Data (판정에 사용될 데이터)

거래소(`context.venue.name`)와 reduce-only 주문 여부(`context.reduceOnly`), 그리고 이 마켓에서 계정에 적용된 레버리지 배율(`context.leverage` — 주문 시점에 Hyperliquid에서 조회한 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-leverage-high-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓의 계정 레버리지가 10배를 넘습니다 — 열거나 추가하기 전에 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has leverage &&
    context.leverage > 10
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-leverage-high-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "maxLeverage": {
      "cedar_long": "10",
      "default": "10x",
      "meaning": "Warn when the host-enriched order-time effective leverage exceeds this. Long, direct > comparison. Placeholder — tune to your risk posture (conservative 3-5x, experienced 5-10x)."
    }
  }
}
```
{% endcode %}

***

**PERP-002: 레버리지가 10배를 초과하는 주문을 넣을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
