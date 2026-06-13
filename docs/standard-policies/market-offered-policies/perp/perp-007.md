---
description: HyperLiquid Order Notional Above USD Cap (WARN)
---

# PERP-007: 주문 명목가가 $10,000을 초과할 시 경고

### Policy Definition (정책 정의)

> HyperLiquid에서 새로 잡는 **주문(Place Order)** 의 명목가가 미화 약 $10,000을 넘는 경우, 주문 규모를 다시 확인하도록 경고합니다.

HyperLiquid perp 주문의 명목가는 수량에 시세를 곱한 값으로, 실제로 시장에 노출되는 금액입니다. 레버리지를 쓰면 적은 증거금으로도 명목가가 크게 불어나기 때문에, 의도보다 큰 규모로 진입하면 시세가 조금만 반대로 가도 손실이 빠르게 커지고 청산까지 밀릴 수 있습니다. 그래서 이번 주문의 명목가가 기준을 넘으면, 정말 이만한 규모로 들어갈 생각이었는지 서명 전에 수량과 가격을 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid에 새로 내는 주문. 포지션을 줄이기만 하는 reduce-only 주문은 제외하고, 명목가가 $10,000을 넘는 진입·추가 주문에만 적용됩니다. (Place Order)

#### Audience (대상 사용자)

HyperLiquid에서 레버리지로 perp를 거래하며 한 번에 큰 포지션을 잡는 사용자

#### Used Data (판정에 사용될 데이터)

주문의 명목가 USD (`context.notionalUsd` — 수량과 시세를 곱해 서버에서 계산한 값), 포지션을 줄이기만 하는 주문인지 여부(`context.reduceOnly`), 거래 장소(`context.venue.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-notional-usd-cap-warn")
@severity("warn")
@reason("이 Hyperliquid 주문의 명목가가 $10,000을 넘습니다 — 나가기 전에 수량과 가격을 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has notionalUsd &&
    context.notionalUsd > 10000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-notional-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "maxNotionalUsd": {
      "cedar_long": "10000",
      "default": "$10,000",
      "meaning": "Warn when the order's USD notional (size × mark price, host-computed integer USD) exceeds this. Placeholder — tune to your typical order size."
    }
  }
}
```
{% endcode %}

***

**PERP-007: 주문 명목가가 $10,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
