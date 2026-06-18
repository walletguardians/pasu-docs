---
description: Adding to a Losing HyperLiquid Position (WARN)
---

# PERP-010: 손실 중인 HyperLiquid 포지션에 물타기할 시 경고

### Policy Definition (정책 정의)

> 이미 20%가 넘게 손실 중인 HyperLiquid 포지션에 **물타기**(추가 진입)를 하는 주문이면, 정말 의도한 것인지 다시 확인하도록 경고합니다.

물타기는 손실 중인 포지션에 같은 방향으로 더 들어가 평균 단가를 낮추는 매매입니다. 평균 단가는 내려가지만 포지션 크기가 커져 방향이 틀렸다면 손실은 그만큼 불어납니다. 손실이 20%를 넘은 포지션에 같은 방향으로 더 얹는 주문이면, 의도한 진입인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid에서 새 주문을 내는 경우에 적용됩니다. 같은 심볼에 이미 열린 포지션이 있고, 그 포지션이 20%가 넘게 손실 중이며, 이번 주문이 포지션을 줄이는 reduce-only가 아닐 때만 발동합니다.&#x20;

#### Audience (대상 사용자)

HyperLiquid에서 perp를 거래하며, 손실 구간에서 물타기로 평균 단가를 낮추곤 하는 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 같은 심볼에 이미 열린 포지션이 있는지 여부
* 포지션의 손익률 %

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-adding-to-loser-warn")
@severity("warn")
@reason("이 Hyperliquid 포지션이 20% 넘게 손실 중입니다 — 손실 포지션에 물타기를 하는 것이니 의도한 것인지 확인하세요")
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
    context.hasOpenPosition == true &&
    context has positionRoeBps &&
    context.positionRoeBps < -2000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-adding-to-loser-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "maxLossBps": {
      "cedar_long": "-2000",
      "default": "-2000 bps (-20%)",
      "meaning": "Warn when adding to an existing position whose return-on-equity (signed bps) is below this. Placeholder — tune to your max-drawdown discipline."
    }
  }
}
```
{% endcode %}

***

PERP-010: 손실 중인 HyperLiquid 포지션에 물타기할 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
