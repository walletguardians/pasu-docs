---
description: Confirm Curve Gauge Weight Vote Before Signing (WARN)
---

# STAKE-011: Curve 게이지 가중치 투표를 제출할 시 경고

### Policy Definition (정책 정의)

> Curve의 **게이지 가중치 투표**를 보내기 전에, 어느 게이지에 얼마를 몰아주는지 확인하도록 경고합니다.

Curve에서는 CRV를 락업해 받은 veCRV로 어느 풀(게이지)에 CRV 보상을 흘려보낼지 투표합니다. 이 가중치는 풀마다 보상 수익률을 좌우해, 투표권을 노린 브라이브(bribe) 대상이 되기도 합니다. 게다가 GaugeController는 같은 게이지에 대한 재투표를 일정 기간 동안 막아둬, 한 번 잘못 넣으면 그 기간이 지날 때까지 되돌릴 수 없습니다. 그래서 dApp이 미리 채워준 게이지 주소나 가중치를 그대로 서명하기 전에, 의도한 게이지와 비중이 맞는지 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

Curve GaugeController에 보내는 게이지 가중치 투표에 적용됩니다. (Vote For Gauge)

#### Audience (대상 사용자)

CRV를 락업해 veCRV를 들고 Curve 게이지 보상 배분에 투표하는 사용자

#### Used Data (판정에 사용될 데이터)

투표를 받는 컨트랙트(`context.venue.name` — `curve_gauge_controller`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("curve-gauge-vote-confirm-warn")
@severity("warn")
@reason("veCRV 게이지 가중치를 배분합니다 — 투표 전에 게이지 주소와 가중치를 확인하세요(투표는 빈도 제한이 있습니다)")
forbid (
    principal,
    action == Staking::Action::"VoteForGauge",
    resource
)
when { context.venue.name == "curve_gauge_controller" };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "curve-gauge-vote-confirm-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "vote_for_gauge"
      }
    }
  }
}
```
{% endcode %}

***

**STAKE-011: Curve 게이지 가중치 투표를 제출할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
