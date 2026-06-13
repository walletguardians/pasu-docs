---
description: Adding to an Existing Position on the Same HyperLiquid Market (WARN)
---

# PERP-011: 이미 포지션이 있는 마켓에 주문을 더 쌓을 시 경고

### Policy Definition (정책 정의)

> 이미 포지션을 들고 있는 HyperLiquid 마켓에 **줄이는 주문이 아닌 새 주문**을 넣어 포지션을 더 쌓는 경우, 의도한 것인지 다시 확인하도록 경고합니다.

HyperLiquid 무기한 선물에서는 같은 마켓에 주문을 거듭 넣으면 포지션이 닫히는 게 아니라 그 위에 계속 더해집니다(stacking). 같은 방향으로 물량을 쌓을수록 명목가가 커지고 청산가가 진입가 쪽으로 바짝 붙어, 시세가 조금만 불리하게 움직여도 한 번에 청산될 수 있습니다. 손실 중인 포지션에 무심코 물량을 더 얹는 물타기로도 흔히 이어집니다. 그래서 이번 주문이 기존 포지션을 줄이는 게 아니라 거기에 더 쌓는 주문이면, 정말 키울 생각이었는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid `/exchange`로 보내는 주문 가운데, 같은 마켓에 이미 포지션이 열려 있고 이번 주문이 포지션을 줄이는 reduce-only 주문이 아닌 경우에 적용됩니다. 포지션을 닫거나 줄이는 주문에는 발동하지 않습니다. (Place Order)

#### Audience (대상 사용자)

HyperLiquid에서 같은 마켓을 여러 번 나눠 들어가거나, 손실 구간에서 물량을 추가하는 사용자

#### Used Data (판정에 사용될 데이터)

이 주문이 포지션을 줄이는 주문인지 여부(`context.reduceOnly` — 주문에서 디코드한 값)와, 해당 마켓에 이미 포지션이 열려 있는지(`context.hasOpenPosition` — HyperLiquid 계정 상태 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-position-stacking-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓에 이미 포지션이 있습니다 — 이 주문이 거기에 더 쌓으니(stacking) 의도한 것인지 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has hasOpenPosition &&
    context.hasOpenPosition == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-position-stacking-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } }
}
```
{% endcode %}

***

**PERP-011: 이미 포지션이 있는 마켓에 주문을 더 쌓을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
