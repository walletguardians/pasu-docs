---
description: Removing Isolated Margin From a Hyperliquid Position (WARN)
---

# PERP-006: 격리 마진을 빼낼 시 경고

### Policy Definition (정책 정의)

> Hyperliquid에서 **격리 마진(isolated margin)** 포지션의 마진을 빼내는 주문이면, 레버리지가 올라가고 청산가가 가까워진다는 점을 다시 확인하도록 경고합니다.

격리 마진은 한 포지션에만 묶어둔 증거금입니다. 이 마진을 줄이면 실효 레버리지가 올라가고, 청산가가 지금 가격 쪽으로 다가옵니다. 그만큼 시세가 조금만 불리하게 움직여도 청산으로 넘어가기 쉬워집니다. 그래서 마진을 빼내는 주문이면, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid 격리 마진 조정 주문 중, 마진을 빼내는(마진 변화량이 음수인) 경우에만 적용됩니다. 마진을 더 넣는 주문에는 발동하지 않습니다. (Adjust Margin)

#### Audience (대상 사용자)

Hyperliquid에서 격리 마진으로 포지션을 굴리며 증거금을 직접 넣고 빼는 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 마진 변화량의 부호

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("isolated-margin-remove-warn")
@severity("warn")
@reason("Hyperliquid에서 격리 마진(isolated margin)을 빼냅니다 — 레버리지가 올라가고 청산가가 가까워지니 마켓과 금액을 확인하세요")
forbid (
    principal,
    action == Perp::Action::"AdjustMargin",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.delta like "-*"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "isolated-margin-remove-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "adjust_margin" } } }
}
```
{% endcode %}

***

**PERP-006: 격리 마진을 빼낼 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
