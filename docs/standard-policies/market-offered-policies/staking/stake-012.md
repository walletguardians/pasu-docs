---
description: Curve Gauge Reward Claim Directed to an Address Other Than Yourself (DENY)
---

# STAKE-012: Curve 게이지 보상을 본인이 아닌 주소로 클레임할 시 차단

### Policy Definition (정책 정의)

> Curve 게이지에서 쌓인 보상을 클레임하면서 받는 주소가 **본인 지갑이 아닌** 경우, 보상이 빠져나가지 않도록 **차단**합니다.

Curve 게이지에 LP 토큰을 예치해두면 CRV를 비롯한 보상이 쌓이고, 보상 클레임(Claim Rewards)으로 이를 지갑으로 받아옵니다. 그런데 Curve의 클레임 함수는 보상을 받을 주소를 따로 지정할 수 있어, 이 칸이 바뀌면 그동안 쌓인 보상이 내가 아닌 다른 주소로 통째로 흘러갑니다. dApp이 채워둔 주소를 그대로 서명하거나, 받는 주소가 슬쩍 바꿔치기된 피싱이면 보상을 고스란히 빼앗깁니다. 보상을 받을 주소가 클레임을 요청한 본인 지갑과 다르면, 서명에 그치지 않고 클레임 자체를 막습니다.

#### Scope (적용 범위)

Curve 게이지의 보상 클레임에 적용됩니다. 보상을 받을 주소가 클레임을 요청한 본인 지갑 주소와 다른 경우 발동합니다. (Claim Rewards)

#### Audience (대상 사용자)

Curve 게이지에 LP 토큰을 예치하고 CRV 등 보상을 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

보상이 클레임되는 곳이 Curve 게이지인지(`context.venue.name`), 보상을 받을 주소(`context.recipient`), 클레임을 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("curve-claim-rewards-recipient-self")
@severity("deny")
@reason("이 Curve 보상 클레임이 본인 지갑이 아닌 주소로 보상을 보냅니다 — 차단했습니다")
forbid (
    principal,
    action == Staking::Action::"ClaimRewards",
    resource
)
when
{
    context.venue.name == "curve_gauge" &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "curve-claim-rewards-recipient-self",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "staking" },
      "action.tag": { "eq": "claim_rewards" }
    }
  }
}
```
{% endcode %}

***

**STAKE-012: Curve 게이지 보상을 본인이 아닌 주소로 클레임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
