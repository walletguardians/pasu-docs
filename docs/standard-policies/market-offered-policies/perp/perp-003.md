---
description: Perp Order at the Market's Maximum Leverage (WARN)
---

# PERP-002: 레버리지가 마켓 최대 등급에 도달한 주문일 시 경고

### Policy Definition (정책 정의)

> HyperLiquid 주문의 레버리지가 해당 마켓이 허용하는 **최대 등급**에 도달한 경우, 청산 위험을 다시 확인하도록 경고합니다.

HyperLiquid는 마켓마다 걸 수 있는 레버리지 상한(maxLeverage)을 따로 정해둡니다. 이 상한까지 레버리지를 끌어올리면 담보 여유가 가장 얇아져, 시세가 조금만 불리하게 움직여도 청산가에 닿습니다. 같은 방향으로 포지션을 새로 열거나 더 키우는 주문일수록 이 위험이 그대로 커집니다. 그래서 주문에 걸린 레버리지가 마켓 최대 등급에 도달했고 reduce-only(포지션 축소 전용)도 아니면, 이만한 레버리지로 들어가도 되는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid에 올리는 주문. reduce-only가 아닌 주문이면서, 걸린 레버리지가 해당 마켓의 최대 등급(maxLeverage)에 도달하거나 넘어서는 경우에 적용됩니다. (PlaceOrder)

#### Audience (대상 사용자)

마켓별 최대 레버리지까지 끌어 쓰며 perp을 공격적으로 운용하는 HyperLiquid 트레이더

#### Used Data (판정에 사용될 데이터)

주문에 걸린 레버리지(`context.leverage`)와 해당 마켓의 최대 허용 등급(`context.maxLeverage` — HyperLiquid /info 서버 조회 결과), 그리고 포지션을 줄이기만 하는 주문인지 나타내는 값(`context.reduceOnly`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-leverage-at-market-max-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓의 레버리지가 마켓 최대 허용 등급에 도달했습니다 — 청산에 가장 민감한 설정이니 열거나 추가하기 전에 확인하세요")
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
    context has maxLeverage &&
    context.leverage >= context.maxLeverage
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-leverage-at-market-max-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } }
}
```
{% endcode %}

***

PERP-002: 레버리지가 마켓 최대 등급에 도달한 주문일 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
