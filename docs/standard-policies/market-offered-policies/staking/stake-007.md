---
description: Aave Staking Rewards Claimed to an Address Other Than Yourself (DENY)
---

# STAKE-007: Aave 스테이킹 보상을 본인이 아닌 주소로 클레임할 시 차단

### Policy Definition (정책 정의)

> Aave Safety Module이나 Umbrella에서 스테이킹 보상을 클레임하는데 받는 주소가 **본인 지갑이 아닌** 경우, 보상이 빠져나가지 않도록 **차단**합니다.

Aave Safety Module(stkAAVE)이나 Umbrella에 토큰을 스테이킹하면 그 대가로 보상이 쌓이고, 클레임(claim)을 통해 보상을 받아갑니다. 그런데 보상을 받을 주소에 내 지갑이 아닌 주소가 입력되면 그동안 쌓인 보상이 엉뚱한 주소로 넘어갑니다. 받는 주소가 바꿔치기된 피싱에 당하지 않도록 받는 주소가 클레임을 요청한 본인 지갑과 다르면 차단합니다.

#### Scope (적용 범위)

Aave Safety Module(stkAAVE)과 Umbrella의 보상 클레임에 적용됩니다. 보상을 받을 주소가 클레임을 요청한 본인 지갑과 다른 경우 발동합니다. (Claim Rewards)

#### Audience (대상 사용자)

Aave Safety Module이나 Umbrella에 스테이킹하고 보상을 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

* 클레임이 일어나는 스테이킹 컨트랙트(`context.prtocol.name`)
* 보상을 받을 주소(`context.recipient`)
* 클레임을 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-claim-rewards-recipient-self")
@severity("deny")
@reason("이 Aave 보상 클레임이 본인 지갑이 아닌 주소로 보상을 보냅니다")
forbid (
    principal,
    action == Staking::Action::"ClaimRewards",
    resource
)
when
{
    ["aave_safety_module",
     "aave_umbrella_rewards_controller"].contains
        (
            context.protocol.name
        ) &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-claim-rewards-recipient-self",
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

**STAKE-007: Aave 스테이킹 보상을 본인이 아닌 주소로 클레임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
