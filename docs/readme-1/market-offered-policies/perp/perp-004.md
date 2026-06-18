---
description: Opening or Adding to a Cross-Margin Position on Hyperliquid (WARN)
---

# PERP-004: 크로스 마진(cross) 마켓에 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> Hyperliquid에서 **크로스 마진(cross)** 마켓에 포지션을 열거나 추가하는 주문이면, 청산 위험 범위를 다시 확인하도록 경고합니다.

크로스 마진(cross)은 계정의 잔액 전체가 한 증거금 풀로 묶입니다. 그래서 크로스로 잡은 포지션 하나가 청산까지 밀리면, 다른 포지션에 쓰던 잔액까지 끌려가 계정이 통째로 비는 일이 생깁니다. 주문이 크로스 마진 마켓을 향하고 새로 포지션을 열거나 늘리는 쪽이면, 한 번의 청산이 계정 전체로 번질 수 있다는 점을 서명 전에 경고합니다.

#### Scope (적용 범위)

Hyperliquid `/exchange`로 보내는 주문 중, 크로스 마진(cross) 마켓에 포지션을 열거나 추가하는 주문에 적용됩니다. 포지션을 줄이는 리듀스온리 주문에는 발동하지 않습니다.

#### Audience (대상 사용자)

Hyperliquid에서 여러 마켓을 동시에 굴리며, 한 포지션의 손실이 계정 전체로 번지는 크로스 마진의 위험을 챙기려는 트레이더

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 마진 방식

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-cross-margin-warn")
@severity("warn")
@reason("이 Hyperliquid 마켓은 크로스 마진(cross)입니다 — 청산 시 계정 잔액 전체가 끌려갈 수 있으니 열거나 추가하기 전에 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has leverageType &&
    context.leverageType == "cross"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-cross-margin-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } }
}
```
{% endcode %}

***

PERP-004: 크로스 마진(cross) 마켓에 주문을 넣을 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
