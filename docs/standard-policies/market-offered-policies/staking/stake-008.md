---
description: Claiming Aave Staking Rewards on Behalf of Another Account (WARN)
---

# STAKE-008: Aave 보상을 본인이 아닌 다른 계정 명의로 클레임할 시 경고

### Policy Definition (정책 정의)

> Aave 보상 클레임의 수령 계정(`onBehalfOf`)이 **서명하는 본인이 아닌** 다른 계정인 경우, 그 계정이 맞는지 확인하도록 경고합니다.

Aave Safety Module(stkAAVE·Umbrella)의 보상 클레임은 누구 명의로 보상을 받을지를 `onBehalfOf` 값으로 따로 지정할 수 있습니다. 이 값을 본인이 아닌 주소로 두면, 쌓아둔 보상이 내 지갑이 아니라 그 계정으로 들어갑니다. dApp이 미리 채워준 값을 그대로 서명하거나, 클레임 화면에서 이 칸이 바꿔치기되면 보상을 통째로 남에게 넘기게 됩니다. 그래서 보상을 받을 계정이 클레임을 서명하는 본인과 다르면, 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

Aave Umbrella 보상 컨트롤러의 보상 클레임에 적용됩니다. 보상을 받을 계정이 클레임을 서명하는 본인 주소와 다른 경우 발동합니다. (Claim Rewards)

#### Audience (대상 사용자)

stkAAVE나 Aave Umbrella에 스테이킹하고 보상을 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

보상을 받을 계정(`context.onBehalfOf`)과 클레임을 서명하는 본인 주소(`principal.address`), 그리고 클레임이 일어나는 컨트랙트(`context.venue.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-claim-on-behalf-of-other")
@severity("warn")
@reason("이 Aave 보상 클레임이 본인이 아닌 다른 계정을 대신해 실행됩니다 — 확인하세요")
forbid(principal, action == Staking::Action::"ClaimRewards", resource)
when {
  context.venue.name == "aave_umbrella_rewards_controller"
  && context has onBehalfOf
  && context.onBehalfOf != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-claim-on-behalf-of-other",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "claim_rewards"
      }
    }
  }
}
```
{% endcode %}

***

**STAKE-008: Aave 보상을 본인이 아닌 다른 계정 명의로 클레임할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
