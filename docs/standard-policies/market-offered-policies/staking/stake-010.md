---
description: Curve Gauge Deposit Credited to Another Address (DENY)
---

# STAKE-010: Curve 게이지에 LP를 본인이 아닌 주소 앞으로 예치할 시 차단

### Policy Definition (정책 정의)

> Curve 게이지에 LP를 스테이킹하면서 그 지분이 **본인이 아닌 주소** 앞으로 잡히는 경우, 예치가 나가지 않도록 **차단**합니다.

Curve 게이지에 LP를 예치하면 스테이킹 보상이 쌓이고, 그 스테이킹 지분은 예치할 때 지정한 주소 앞으로 기록됩니다. 게이지의 예치 함수는 이 받는 주소를 따로 지정할 수 있어, 본인이 아닌 주소를 넣으면 스테이킹한 LP가 통째로 그 주소 소유가 됩니다. 보상도 그 주소가 받고, 인출도 그 주소만 할 수 있어 본인은 예치한 LP를 되찾지 못합니다. dApp이 채워준 주소를 그대로 서명했다가, 또는 받는 주소 칸이 바꿔치기된 피싱에 걸려 LP를 통째로 넘기는 일이 생깁니다. 예치를 받는 주소가 본인 지갑과 다르면, 서명 전에 차단합니다.

#### Scope (적용 범위)

Curve 게이지 예치에 적용됩니다. 예치를 받는 주소가 예치를 요청한 본인 지갑과 다른 경우 발동합니다. (Gauge Deposit)

#### Audience (대상 사용자)

Curve 풀에 유동성을 넣고 게이지에 LP를 스테이킹하는 사용자

#### Used Data (판정에 사용될 데이터)

예치를 진행하는 게이지(`context.venue.name`), 지분을 받을 주소(`context.onBehalfOf`)와 예치를 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("curve-gauge-deposit-for-other-deny")
@severity("deny")
@reason("이 게이지 예치가 스테이킹한 LP를 본인이 아닌 주소 앞으로 잡습니다 — 그 주소가 통제·인출할 수 있어 차단했습니다")
forbid (
    principal,
    action == Staking::Action::"GaugeDeposit",
    resource
)
when
{
    context.venue.name == "curve_gauge" &&
    context has onBehalfOf &&
    context.onBehalfOf != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "curve-gauge-deposit-for-other-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "gauge_deposit"
      }
    }
  }
}
```
{% endcode %}

***

**STAKE-010: Curve 게이지에 LP를 본인이 아닌 주소 앞으로 예치할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
