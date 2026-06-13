---
description: New Order While Account Is Down 8% From Its High-Water Mark (WARN)
---

# PERP-015: 계정이 고점 대비 8% 넘게 빠진 상태에서 신규 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> Hyperliquid 계정 자산이 최고점 대비 8% 이상 빠진 상태에서 포지션을 더하는 **신규 주문**을 넣는 경우, 잠시 멈추고 다시 확인하도록 경고합니다.

Hyperliquid는 미실현 손익까지 포함한 계정 자산(equity)이 이제껏 찍은 최고점에서 얼마나 내려왔는지를 추적합니다. 이 낙폭을 트레일링 드로다운(trailing drawdown)이라 하고, prop 펌(prop firm)에서는 보통 이 값이 일정 선을 넘으면 계정을 정지시킵니다. 한창 물려서 자산이 고점 대비 8% 이상 빠진 상황은 판단이 흐려지기 쉬운 때라, 여기서 리스크를 더 얹는 신규 진입은 손실을 키우거나 계정 한도를 넘겨버리기 쉽습니다. 그래서 낙폭이 8%에 이른 상태에서 포지션을 줄이지 않고 새로 키우는 주문이면, 지금 더 들어가는 게 맞는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid 신규 주문(PlaceOrder). 포지션을 줄이는 reduce-only 주문은 빠지고, 계정 자산이 고점 대비 8%(800bp) 이상 빠진 상태에서 포지션을 더하는 주문에만 적용됩니다. (Place Order)

#### Audience (대상 사용자)

Hyperliquid에서 perp을 굴리며 손실 구간에서 물타기로 끌고 가기 쉬운 사용자, 또는 트레일링 드로다운 한도가 걸린 prop 펌 계정 운용자

#### Used Data (판정에 사용될 데이터)

이번 주문이 포지션을 줄이는 reduce-only인지 여부(`context.reduceOnly`)와, 계정 자산이 고점 대비 몇 bp 빠졌는지를 나타내는 낙폭(`context.custom.peakDrawdownBps` — Hyperliquid 계정 상태를 서버에서 조회해 계산한 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-max-drawdown-warn")
@severity("warn")
@reason("Hyperliquid 계정이 최고점(high-water mark) 대비 8% 아래입니다 — 리스크를 더하기 전에 확인하세요(트레일링 드로다운 서킷 브레이커)")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has custom &&
    context.custom has peakDrawdownBps &&
    context.custom.peakDrawdownBps >= 800
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-max-drawdown-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "policy_rpc": [
    {
      "id": "equity-drawdown",
      "method": "perp.equity_drawdown_bps",
      "params": { "chain_id": "$.root.chain_id" },
      "outputs": [
        {
          "kind": "context",
          "field": "peakDrawdownBps",
          "type": "Long",
          "from": "$.result.peakDrawdownBps"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "peakDrawdownBps": "Long" } },
  "_template": {
    "maxPeakDrawdownBps": {
      "cedar_long": "800",
      "default": "800 bps (8% trailing)",
      "meaning": "Warn when account equity is down this many bps from its high-water mark at order time. Prop-firm trailing max-drawdown (Propr / Breakout 2-step) = 8%; static-rule variant = 600 bps (6%). Measured on EQUITY (unrealized PnL included) vs a server-persisted high-water mark. Placeholder — tune to your rulebook."
    }
  }
}
```
{% endcode %}

***

**PERP-015: 계정이 고점 대비 8% 넘게 빠진 상태에서 신규 주문을 넣을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
