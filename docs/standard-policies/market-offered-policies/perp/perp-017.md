---
description: Excessive Number of Trades on Hyperliquid in a Single Day (WARN)
---

# PERP-017: 오늘 Hyperliquid 거래가 15건을 초과할 시 경고

### Policy Definition (정책 정의)

> 오늘 Hyperliquid에서 체결한 거래가 15건을 넘은 상태에서 새 주문을 내는 경우, 과도한 매매가 아닌지 다시 확인하도록 경고합니다.

perp 거래소는  흥분하거나 손실을 만회하려다 보면 하루에도 수십 번씩 진입과 청산을 반복하기 쉽습니다. 이렇게 거래 횟수가 늘수록 매번 수수료가 빠져나가고, 충동적으로 들어간 자리일수록 수익률은 더 나빠지는 경향이 있습니다. 그래서 오늘 체결한 거래 수를 세어, 그 수가 15건을 넘은 뒤 또 새 주문을 내려 하면 서명 전에 한 번 짚어보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에 새 주문을 내는 경우. 오늘 체결한 거래가 15건을 넘었고, 이번 주문이 포지션을 줄이는 reduce-only 청산이 아닐 때만 적용됩니다.

#### Audience (대상 사용자)

Hyperliquid에서 단타로 자주 들어갔다 나오며, 손실 뒤 충동 매매로 거래가 늘어나기 쉬운 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 오늘 체결한 거래 수

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-overtrading-warn")
@severity("warn")
@reason("오늘 Hyperliquid 거래가 15건을 넘습니다 — 과도한 매매가 아닌지 확인하세요")
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
    context.custom has tradesToday &&
    context.custom.tradesToday > 15
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-overtrading-warn",
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
          "field": "tradesToday",
          "type": "Long",
          "from": "$.result.tradesToday"
        }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "tradesToday": "Long" } },
  "_template": {
    "maxTradesToday": {
      "cedar_long": "15",
      "default": "15 trades/day",
      "meaning": "Warn when filled trades since today's session anchor exceed this. HEURISTIC placeholder (no rulebook number; trading frequency → worse returns is evidence-backed, the cutoff is not). Set to your own normal-day baseline."
    }
  }
}
```
{% endcode %}

***

**PERP-017: 오늘 Hyperliquid 거래가 15건을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
