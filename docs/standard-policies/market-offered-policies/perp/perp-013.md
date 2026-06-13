---
description: Placing a New Order While Margin Usage Already Exceeds 50% (WARN)
---

# PERP-013: 마진 사용률이 50%를 초과한 상태에서 주문할 시 경고

### Policy Definition (정책 정의)

> Hyperliquid 계정의 **마진 사용률**이 이미 50%를 넘은 상태에서 노출을 더 늘리는 주문을 내면, 추가 진입 전에 한 번 더 확인하도록 경고합니다.

Hyperliquid perp는 계정 자산을 담보(마진)로 잡고 포지션을 엽니다. 이미 들어간 마진이 계정 가치의 절반을 넘었다면 남은 여유가 얼마 없다는 뜻이고, 여기서 포지션을 더 키우면 시세가 조금만 불리하게 움직여도 청산 가격에 빠르게 닿습니다. 그래서 새 주문을 내는 시점에 서버에서 조회한 계정 마진 사용률이 50%를 넘으면, 노출을 더 늘려도 괜찮은 상황인지 서명 전에 확인하도록 경고합니다. 포지션을 줄이는 reduce-only 주문은 노출을 키우지 않으므로 발동하지 않습니다.

#### Scope (적용 범위)

Hyperliquid에 새 주문을 넣을 때 적용됩니다. 포지션을 줄이는 reduce-only 주문은 제외되고, 주문 시점의 계정 마진 사용률이 50%(5,000bp)를 넘는 경우에만 발동합니다. (Place Order)

#### Audience (대상 사용자)

Hyperliquid에서 여러 포지션을 동시에 운용하며 마진을 빠듯하게 쓰는 사용자

#### Used Data (판정에 사용될 데이터)

reduce-only 주문 여부(`context.reduceOnly`)와, 주문 시점의 계정 마진 사용률(`context.marginUsedRatioBps` — 들어간 마진을 계정 가치로 나눈 비율을 bp로 환산한 값, 서버 조회 결과)입니다. 해당 마켓의 최대 레버리지(`context.maxLeverage`)가 함께 조회됐을 때만 비교가 이뤄집니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("order-margin-health-warn")
@severity("warn")
@reason("Hyperliquid 계정 마진의 절반 이상을 이미 쓰고 있습니다 — 노출을 더 늘리기 전에 확인하세요")
forbid (
    principal,
    action == Perp::Action::"PlaceOrder",
    resource
)
when
{
    context.venue.name == "hyperliquid" &&
    context.reduceOnly == false &&
    context has maxLeverage &&
    context has marginUsedRatioBps &&
    context.marginUsedRatioBps > 5000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "order-margin-health-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "place_order" } } },
  "_template": {
    "maxMarginUsedBps": {
      "cedar_long": "5000",
      "default": "5000 bps (50%)",
      "meaning": "Warn when account-wide margin utilization (totalMarginUsed / accountValue, bps) exceeds this at order time. Placeholder — tune to your buffer comfort."
    }
  }
}
```
{% endcode %}

***

**PERP-013: 마진 사용률이 50%를 초과한 상태에서 주문할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: HyperLiquid_
