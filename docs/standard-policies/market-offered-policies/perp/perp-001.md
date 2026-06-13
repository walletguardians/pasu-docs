---
description: Setting Hyperliquid Leverage Above 10x (WARN)
---

# PERP-001: 레버리지를 10배 초과로 설정할 시 경고

### Policy Definition (정책 정의)

> Hyperliquid에서 **레버리지(leverage)** 를 10배보다 높게 설정하는 경우, 청산 위험을 다시 확인하도록 경고합니다.

레버리지는 내 증거금보다 큰 포지션을 잡게 해주는 배율입니다. 배율이 높을수록 같은 증거금으로 더 큰 포지션을 들고 갈 수 있지만, 청산가도 현재가 바로 코앞까지 따라붙습니다. 10배를 넘기면 가격이 반대로 몇 %만 움직여도 증거금이 다 깎여 포지션이 청산됩니다. 그래서 설정하려는 레버리지가 10배를 넘으면 이 배율로 가도 괜찮은지 주문 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Hyperliquid에서 마켓의 레버리지 배율을 바꾸는 주문. 새로 설정하는 레버리지가 10배를 초과하는 경우에 적용됩니다. (Change Leverage)

#### Audience (대상 사용자)

Hyperliquid에서 perp을 거래하며 마켓별 레버리지를 직접 조정하는 사용자

#### Used Data (판정에 사용될 데이터)

거래소(`context.venue.name`)와 이번에 설정하는 레버리지 배율(`context.newLeverage`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("update-leverage-cap-warn")
@severity("warn")
@reason("Hyperliquid 레버리지를 10배 넘게 설정합니다 — 작은 역방향 움직임에도 청산될 수 있으니 확인하세요")
forbid (
    principal,
    action == Perp::Action::"ChangeLeverage",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.newLeverage.greaterThan(decimal("10.0"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "update-leverage-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "change_leverage" } } },
  "_template": {
    "maxLeverage": {
      "cedar_decimal": "10.0",
      "default": "10.0x",
      "meaning": "Warn when the leverage being SET exceeds this (Cedar decimal, .greaterThan comparison). Placeholder — tune to your risk posture."
    }
  }
}
```
{% endcode %}

***

**PERP-001: 레버리지를 10배 초과로 설정할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
