---
description: New Order After Today's Realized Loss Hits $500 (WARN)
---

# PERP-020: 오늘 실현 손실이 $500에 이른 뒤 새 주문을 낼 시 경고

### Policy Definition (정책 정의)

> 오늘 Hyperliquid에서 확정된 실현 손실이 $500에 이른 상태에서 리스크를 더하는 새 주문을 내면, 멈추고 확인하도록 경고합니다.

실현 손실은 포지션을 닫아 실제로 확정된 손실입니다. Hyperliquid은 오늘(UTC 기준) 청산해 확정한 손익을 합산해 보여주는데, 이 합이 $500 손실에 이르면 그날은 이미 꽤 잃은 날입니다. 손실이 쌓인 날일수록 본전을 찾으려고 무리한 주문을 내다가 손실을 더 키우기 쉽습니다. 확정 손실이 $500에 닿은 상태에서 새로 리스크를 더하는 주문이면, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에 새 주문을 내는 경우. 오늘 확정한 실현 손익이 $500 손실 이하이고, 그 주문이 포지션을 줄이는 reduce-only 주문이 아닐 때 적용됩니다. (Place Order)

#### Audience (대상 사용자)

Hyperliquid 무기한 선물 거래를 자주 하며, 손실이 난 날 본전 생각에 매매를 이어가기 쉬운 사용자

#### Used Data (판정에 사용될 데이터)

* 오늘 확정한 실현 손익
* 포지션을 줄이는 reduce-only 주문인지 여부

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-daily-realized-loss-warn")
@severity("warn")
@reason("오늘 Hyperliquid 실현 손실이 $500입니다 — 리스크를 더하기 전에 확인하세요(일일 실현 손실 한도)")
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
    context.custom has realizedPnlTodayUsd &&
    context.custom.realizedPnlTodayUsd <= -500
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-daily-realized-loss-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "policy_rpc": [
    {
      "id": "session-fill-stats",
      "method": "perp.session_fill_stats",
      "params": { "chain_id": "$.root.chain_id" },
      "outputs": [
        {
          "kind": "context",
          "field": "realizedPnlTodayUsd",
          "type": "Long",
          "from": "$.result.realizedPnlTodayUsd"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "realizedPnlTodayUsd": "Long" } },
  "_template": {
    "maxDailyRealizedLossUsd": {
      "cedar_long": "-500",
      "default": "-500 ($500/day realized loss)",
      "meaning": "Warn when today's signed realized PnL (UTC day-start, whole USD) is at or below this — i.e. you've booked >= $500 in net realized losses. PLACEHOLDER personal budget, not a rulebook number; funding excluded (trade closedPnl only). Set to your own daily stop."
    }
  }
}
```
{% endcode %}

***

**PERP-020: 오늘 실현 손실이 $500에 이른 뒤 새 주문을 낼 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
