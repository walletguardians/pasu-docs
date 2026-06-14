---
description: Opening a Position on a Market Outside the Allowlist (WARN)
---

# PERP-008: 허용 목록에 없는 마켓에 주문할 시 경고

### Policy Definition (정책 정의)

> 미리 정해둔 거래 마켓 목록에 없는 마켓에서 새 포지션을 여는 주문이면, 마켓이 맞는지 다시 확인하도록 경고합니다.

내가 실제로 거래하는 마켓만 허용 목록에 적어두고, 그 밖의 마켓에 새로 진입하는 주문을 걸러냅니다. 낯선 마켓은 유동성이 얇아 체결가가 크게 밀리거나, 심볼을 잘못 골라 의도와 다른 자산에 베팅하게 될 위험이 있습니다. 그래서 허용 목록에 없는 마켓에서 포지션을 새로 여는 주문이면, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에서 포지션을 새로 열거나 늘리는 주문에 적용됩니다. 허용 목록에 적힌 마켓이면 통과하고, 기존 포지션을 줄이는 reduce-only 주문에는 발동하지 않습니다.

#### Audience (대상 사용자)

거래하는 마켓을 몇 개로 좁혀두고자 하는 Hyperliquid 트레이더

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 마켓 심볼의 허용 목록(`BTC`, `ETH`, `SOL`, `HYPE`)
* 주문이 들어가는 마켓

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-symbol-not-allowlisted-warn")
@severity("warn")
@reason("허용 목록에 없는 Hyperliquid 마켓에서 포지션을 엽니다 — 낯설거나 유동성 낮은 마켓은 위험이 크니 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    !(["BTC", "ETH", "SOL", "HYPE"].contains(context.market.symbol))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-symbol-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "marketAllowlist": {
      "cedar_set": "[\"BTC\", \"ETH\", \"SOL\", \"HYPE\"]",
      "default": "BTC, ETH, SOL, HYPE",
      "meaning": "Warn on any order whose market symbol is NOT in this set. Use HL UNIVERSE names, matched exactly (case-sensitive; memecoin perps are k-prefixed: kPEPE not PEPE). Placeholder majors — replace with the markets you actually trade (every market you use MUST be listed or ~every order warns)."
    }
  }
}
```
{% endcode %}

***

**PERP-008: 허용 목록에 없는 마켓에 주문할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
