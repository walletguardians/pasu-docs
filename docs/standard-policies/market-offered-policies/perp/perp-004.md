---
description: Alt-Market Order Above 5x Leverage on HyperLiquid (WARN)
---

# PERP-004: 알트 마켓을 5배 넘는 레버리지로 주문할 시 경고

### Policy Definition (정책 정의)

> BTC·ETH·SOL이 아닌 **알트 마켓**에서 5배를 넘는 레버리지로 새 포지션을 열면, 변동성을 다시 확인하도록 경고합니다.

알트 마켓은 BTC·ETH·SOL 같은 메이저보다 가격이 훨씬 거칠게 움직여서, 5배만 걸어도 큰 변동성 때문에 청산까지 닿기 쉽습니다. 알트 마켓에서 5배를 넘는 레버리지로 주문을 새로 넣으면, 이 마켓에 이 배율이 맞는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

HyperLiquid에서 새 포지션을 여는 주문에 적용됩니다. 마켓이 BTC·ETH·SOL이 아니고, 레버리지가 5배를 넘는 경우에만 발동하며, 포지션을 줄이거나 닫는 reduce-only 주문에는 발동하지 않습니다.

#### Audience (대상 사용자)

HyperLiquid에서 변동성 큰 알트 마켓을 높은 배율로 거래하는 트레이더

#### Used Data (판정에 사용될 데이터)

* 프로토콜&#x20;
* 주문 레버리지 배율

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-alt-leverage-warn")
@severity("warn")
@reason("알트 마켓 Hyperliquid 포지션을 5배 넘는 레버리지로 엽니다 — 알트는 변동이 격해 설정한 알트 레버리지 한도에 걸렸으니 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    !(["BTC", "ETH", "SOL"].contains(context.market.symbol)) &&
    context has leverage &&
    context.leverage > 5
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-alt-leverage-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "majorsList": {
      "cedar_set": "[\"BTC\", \"ETH\", \"SOL\"]",
      "default": "BTC, ETH, SOL",
      "meaning": "Markets EXEMPT from the alt leverage cap (the global cap in order-leverage-high-warn still applies to them). Use HL universe names, case-sensitive (kPEPE not PEPE)."
    },
    "altMaxLeverage": {
      "cedar_long": "5",
      "default": "5x",
      "meaning": "Warn when effective leverage on a NON-majors market exceeds this. Placeholder — the classic two-tier desk rule (10x majors / 5x alts)."
    }
  }
}
```
{% endcode %}

***

**PERP-004: 알트 마켓을 5배 넘는 레버리지로 주문할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: HyperLiquid_
