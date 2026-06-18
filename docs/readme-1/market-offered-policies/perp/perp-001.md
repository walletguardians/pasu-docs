---
description: Placing a Perp Order Above 10x Leverage (WARN)
---

# PERP-001: 레버리지가 10배를 초과하는 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> **Hyperliquid 마켓**의 계정 레버리지가 10배를 넘는 상태에서 포지션을 열거나 늘리는 주문이면, 청산 위험을 다시 확인하도록 경고합니다.

레버리지가 높을수록 같은 증거금으로 더 큰 포지션을 들고 갈 수 있지만, 청산가도 현재가 바로 코앞까지 따라붙습니다. 10배를 넘기면 가격이 반대로 몇 %만 움직여도 증거금이 다 깎여 포지션이 청산됩니다. 진입하려는 레버리지가 10배를 넘으면 주문 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid의 perp 주문. 마켓 레버리지가 10배를 넘고, 포지션을 줄이는 reduce-only 주문이 아닐 때 적용됩니다.

#### Audience (대상 사용자)

Hyperliquid에서 perp을 거래하며 고배율 레버리지로 포지션을 잡는 사용자

#### Used Data (판정에 사용될 데이터)

* 계정에 적용된 레버리지 배율

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

PERP-001: 레버리지가 10배를 초과하는 주문을 넣을 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
