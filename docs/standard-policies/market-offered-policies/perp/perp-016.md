---
description: Cooldown After a Streak of Losing Trades (WARN)
---

# PERP-016: 연속 손실이 3번 이어진 뒤 주문을 넣을 시 경고

### Policy Definition (정책 정의)

> HyperLiquid에서 오늘 손실로 닫은 거래가 **3번 연속**으로 이어진 직후 새 주문을 넣는 경우, 잠시 멈추도록 경고합니다.

연달아 손실을 보면 잃은 돈을 빨리 메우려고 평소보다 크게, 자주 베팅하기 쉽습니다. 이런 보복 매매는 손실을 더 키우는 쪽으로 가는 경우가 많습니다. 그래서 오늘 닫은 거래 중 손실이 연속으로 3번 쌓이면, 다음 주문을 내기 전에 잠깐 멈추고 지금 진입이 흥분에서 나온 건 아닌지 돌아보도록 경고합니다. 너무 작은 손익(기본 $1 미만)으로 닫은 거래는 연속 손실로 세지 않고, 그 사이에 한 번이라도 이익으로 닫으면 연속 기록이 0으로 돌아갑니다.

#### Scope (적용 범위)

HyperLiquid에 새로 내는 주문. 포지션을 줄이기만 하는 reduce-only 주문은 제외하고, 오늘 손실로 닫은 거래가 3번 연속 이어진 상태에서 내는 진입·추가 주문에만 적용됩니다. (Place Order)

#### Audience (대상 사용자)

HyperLiquid에서 perp을 단타로 자주 돌리며, 손실 뒤 곧바로 다시 들어가는 버릇이 있는 사용자

#### Used Data (판정에 사용될 데이터)

오늘 손실로 닫은 거래가 연속 몇 번째인지(`context.custom.lossStreak` — 주문 시점에 HyperLiquid 체결 기록을 조회해 센 결과로, 손실 $1 미만으로 닫은 거래는 빼고 계산), 거래소(`context.venue.name`)와 포지션을 줄이기만 하는 주문인지 여부(`context.reduceOnly`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-loss-streak-cooldown-warn")
@severity("warn")
@reason("Hyperliquid에서 3연속 손실 거래입니다 — 다음 주문 전에 잠시 멈추세요(보복 매매 쿨다운)")
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
    context.custom has lossStreak &&
    context.custom.lossStreak >= 3
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-loss-streak-cooldown-warn",
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
          "field": "lossStreak",
          "type": "Long",
          "from": "$.result.lossStreak"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "lossStreak": "Long" } },
  "_template": {
    "minLossStreak": {
      "cedar_long": "3",
      "default": "3 consecutive losing trades (range 3–5)",
      "meaning": "Warn when the account's most-recent run of consecutive losing closed trades TODAY reaches this. HEURISTIC placeholder (no rulebook number; the behavior — post-loss risk escalation / tilt — is evidence-backed, the cutoff is not). Lower = more cautious."
    },
    "minLossUsd": {
      "param": "min_loss_usd",
      "default": "1",
      "meaning": "Minimum realized loss (USD) for a closed trade to count toward the streak — sub-threshold scratch closes are invisible (neither extend nor reset the streak), and a win must clear +$min to reset. Per-policy via the policy_rpc `min_loss_usd` literal param (default $1)."
    }
  }
}
```
{% endcode %}

***

**PERP-016: 연속 손실이 3번 이어진 뒤 주문을 넣을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
