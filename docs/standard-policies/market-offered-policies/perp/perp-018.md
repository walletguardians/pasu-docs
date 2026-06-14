---
description: New Order After Five Losing Trades Today (WARN)
---

# PERP-018: 오늘 손실 거래가 5건에 도달한 뒤 새 주문을 낼 시 경고

### Policy Definition (정책 정의)

> 오늘 Hyperliquid에서 손실로 마감한 거래가 **5건**에 이른 뒤 새 포지션을 여는 주문을 내면, 리스크를 더하기 전에 확인하도록 경고합니다.

연달아 손실을 보는 날은 판단이 흐트러지기 쉽고, 잃은 돈을 한 번에 되찾으려다 평소보다 큰 포지션을 던지는 일이 잦습니다. 오늘 손실로 닫은 거래 수가 5건에 닿은 상태에서 또 새로 포지션을 여는 주문이면 확인하도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에 새 포지션을 여는 주문(PlaceOrder)에 적용됩니다. 오늘 손실로 마감한 거래가 5건 이상이고, 이번 주문이 포지션을 줄이는 reduce-only가 아닌 경우에 발동합니다.

#### Audience (대상 사용자)

Hyperliquid perp을 단타로 굴리며 손실 본 날 추격 매매ㄹ트레이더

#### Used Data (판정에 사용될 데이터)

오늘(UTC 기준 하루) 손실로 마감한 거래 수(`context.custom.lossesToday` — Hyperliquid 체결 내역을 서버에서 조회해 집계한 결과). 실현 손실이 $1 미만인 자잘한 청산은 세지 않습니다(`min_loss_usd`). 그리고 이번 주문이 reduce-only인지 여부(`context.reduceOnly`).

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-daily-loss-count-warn")
@severity("warn")
@reason("오늘 Hyperliquid에서 손실 거래가 5건입니다 — 리스크를 더하기 전에 확인하세요(일일 손실 횟수 서킷 브레이커)")
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
    context.custom has lossesToday &&
    context.custom.lossesToday >= 5
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-daily-loss-count-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "policy_rpc": [
    {
      "id": "session-fill-stats",
      "method": "perp.session_fill_stats",
      "params": { "chain_id": "$.root.chain_id", "min_loss_usd": "1" },
      "outputs": [
        {
          "kind": "context",
          "field": "lossesToday",
          "type": "Long",
          "from": "$.result.lossesToday"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "lossesToday": "Long" } },
  "_template": {
    "minDailyLossCount": {
      "cedar_long": "5",
      "default": "5 losing trades/day",
      "meaning": "Warn when the account's count of losing closed trades today (UTC day-start) reaches this. HEURISTIC placeholder (no rulebook number; loss-frequency churning is evidence-backed, the cutoff is not). Set to your own baseline."
    },
    "minLossUsd": {
      "param": "min_loss_usd",
      "default": "1",
      "meaning": "Minimum realized loss (USD) for a closed trade to count toward lossesToday — sub-threshold scratch closes are ignored. Per-policy via the policy_rpc `min_loss_usd` literal param (default $1)."
    }
  }
}
```
{% endcode %}

***

**PERP-018: 오늘 손실 거래가 5건에 도달한 뒤 새 주문을 낼 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
