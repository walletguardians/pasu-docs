---
description: New Short Position While Long-Only Is Set (WARN)
---

# PERP-010: 롱 전용 설정에서 새 숏 포지션을 열 시 경고

### Policy Definition (정책 정의)

> Hyperliquid에서 **롱 전용(long-only)** 으로 정해 둔 계정이 새로 **숏 포지션**을 여는 주문을 내면, 의도한 매매인지 다시 확인하도록 경고합니다.

롱만 잡기로 정한 사람에게 숏 주문은 보통 둘 중 하나입니다. 시장 방향을 잘못 읽고 반대로 내는 실수이거나, 에이전트 키가 털려 내 의도와 무관하게 들어오는 주문입니다. 숏은 가격이 오르면 손실이 무한히 커질 수 있어 롱보다 위험을 통제하기 까다롭습니다. 그래서 기존 포지션을 줄이는 reduce-only 주문이 아니라 새 숏을 여는 주문이면, 정말 방향을 바꿔 매매하려는 것인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid 무기한 선물(perp) 주문 중, 기존 포지션을 줄이는 게 아니라 새로 숏 방향으로 진입하는 주문에 적용됩니다. 같은 매매라도 포지션을 닫거나 줄이는 reduce-only 주문에는 발동하지 않습니다. (Place Order)

#### Audience (대상 사용자)

매수(롱)만 하기로 매매 규칙을 정해 둔 사용자, 또는 에이전트 키로 자동매매를 돌리며 의도치 않은 숏 진입을 막고 싶은 사용자

#### Used Data (판정에 사용될 데이터)

주문을 낸 거래소(`context.venue.name`), 주문 방향이 숏인지(`context.side`), 기존 포지션을 줄이는 reduce-only 주문인지(`context.reduceOnly`), 그리고 레버리지 정보(`context has maxLeverage`)가 실린 실제 perp 주문인지 — 모두 주문 내용을 그 자리에서 해석해 얻은 값입니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-no-new-short-warn")
@severity("warn")
@reason("Hyperliquid에서 숏 포지션을 엽니다 — 롱 전용(long-only) 설정에 걸렸으니 의도한 것인지 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context.side == "short" &&
    context has maxLeverage
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-no-new-short-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } }
}
```
{% endcode %}

***

**PERP-010: 롱 전용 설정에서 새 숏 포지션을 열 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
