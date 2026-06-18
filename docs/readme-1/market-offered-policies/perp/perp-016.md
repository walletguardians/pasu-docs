---
description: New Order While Account Is Down 5% On the Day (WARN)
---

# PERP-016: 오늘 계정이 5% 넘게 손실 난 상태에서 신규 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> 오늘 하루 Hyperliquid 계정 자산이 5% 넘게 빠진 상태에서 포지션을 더하는 **신규 주문**을 넣는 경우, 잠시 멈추고 다시 확인하도록 경고합니다.

Hyperliquid는 미실현 손익까지 포함한 계정 자산(equity)이 오늘 장 시작 기준에서 얼마나 내려왔는지를 추적합니다. 오늘만 자산이 5% 넘게 빠진 날은 이미 한 방향으로 크게 밀린 날이라, 여기서 손실을 만회하려 더 들어가는 신규 진입은 그날 손실을 더 키우거나 일일 한도를 넘겨버리기 쉽습니다.오늘 낙폭이 5%를 넘은 상태에서 새로운 주문을 넣으면 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid 신규 주문(PlaceOrder). 포지션을 줄이는 reduce-only 주문은 빠지고, 오늘 장 시작 대비 계정 자산이 5%(500bp) 넘게 빠진 상태에서 포지션을 더하는 주문에만 적용됩니다.&#x20;

#### Audience (대상 사용자)

Hyperliquid에서 perp을 굴리며 손실 난 날 만회 매매로 더 밀어붙이기 쉬운 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 오늘 장 시작 대비 계정 자산이 몇 bp 빠졌는지를 나타내는 낙폭

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-daily-loss-limit-warn")
@severity("warn")
@reason("오늘 Hyperliquid 계정이 5% 손실입니다 — 리스크를 더하기 전에 확인하세요(일일 손실 서킷 브레이커)")
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
    context.custom has dayDrawdownBps &&
    context.custom.dayDrawdownBps >= 500
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-daily-loss-limit-warn",
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
          "field": "dayDrawdownBps",
          "type": "Long",
          "from": "$.result.dayDrawdownBps"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "dayDrawdownBps": "Long" } },
  "_template": {
    "maxDayDrawdownBps": {
      "cedar_long": "500",
      "default": "500 bps (5%)",
      "meaning": "Warn when account equity is down this many bps from today's baseline at order time. Prop-firm headline daily loss = 5%; strict (1-step / aggressive) = 300 bps (3%). Measured on EQUITY (unrealized PnL included) vs a server-persisted day-start baseline. Placeholder — tune to your rulebook."
    }
  }
}
```
{% endcode %}

***

PERP-016: 오늘 계정이 5% 넘게 손실 난 상태에서 신규 주문을 넣을 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
