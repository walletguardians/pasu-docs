---
description: Reduce-Only Lockdown Blocking Position-Increasing Orders (DENY)
---

# PERP-022: 리듀스온리(reduce-only) 잠금 중 포지션을 늘리는 주문을 낼 시 차단

### Policy Definition (정책 정의)

> 리듀스온리(reduce-only) 잠금을 켜둔 상태에서 HyperLiquid에 **포지션을 줄이지 않는 주문**을 내면 차단합니다.

HyperLiquid 주문에는 리듀스온리(reduce-only) 플래그가 있어, 이 플래그가 붙은 주문은 지금 들고 있는 포지션을 줄이거나 닫기만 할 수 있습니다. 리듀스온리 잠금은 이 플래그가 없는 주문, 즉 새 포지션을 열거나 기존 포지션을 키울 수 있는 주문을 전부 막아두는 모드입니다. 손실을 키우지 않고 정리만 하려고 잠가뒀는데 키가 탈취당하거나 실수로 매수·매도 주문이 나가면, 줄이려던 포지션이 도리어 불어날 수 있습니다. 따라서 이 잠금이 켜져 있는 동안 리듀스온리가 아닌 주문이 나가면 서명 전에 차단합니다.

#### Scope (적용 범위)

HyperLiquid 주문 제출(PlaceOrder)에 적용됩니다. 주문에 리듀스온리(reduce-only) 플래그가 붙어 있지 않은 경우에만 발동하고, 포지션을 줄이거나 닫는 리듀스온리 주문은 막지 않습니다. (Place Order)

#### Audience (대상 사용자)

포지션을 정리하는 중이라 한동안 새 매수·매도를 막아두고 싶은 HyperLiquid 사용자

#### Used Data (판정에 사용될 데이터)

주문이 나가는 거래소(`context.venue.name` — HyperLiquid인지)와 주문에 리듀스온리 플래그가 붙어 있는지 여부(`context.reduceOnly`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-reduce-only-lockdown-deny")
@severity("deny")
@reason("리듀스온리(reduce-only) 잠금이 켜져 있어 포지션을 줄이는 주문만 허용됩니다 — 차단했습니다")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-reduce-only-lockdown-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } }
}
```
{% endcode %}

***

**PERP-022: 리듀스온리(reduce-only) 잠금 중 포지션을 늘리는 주문을 낼 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
