---
description: Aave Stake Redemption Sent to an Address Other Than Yourself (DENY)
---

# STAKE-004: Aave 스테이킹 자산을 본인이 아닌 주소로 인출할 시 차단

### Policy Definition (정책 정의)

> Aave 스테이킹에서 자산을 인출(Redeem)하면서 받는 주소가 **본인 지갑이 아닌** 경우, 인출이 나가지 않도록 **차단**합니다.

Aave Safety Module(stkAAVE)·sGHO·Umbrella에 자산을 맡기면 프로토콜이 위기일 때 일부가 슬래싱될 수 있는 대신 그 보상을 받습니다. 그동안 묶어둔 원금을 다시 찾는 동작이 인출(Redeem)인데, 이 함수는 자금을 받을 주소를 따로 지정할 수 있어 주소가 바뀌면 빼낸 원금이 내가 아닌 엉뚱한 곳으로 흘러갑니다. dApp이 미리 채워준 주소를 그대로 서명하거나, 받는 주소 칸이 슬쩍 바꿔치기된 피싱에 당하면 한 번에 원금을 통째로 잃습니다. 받는 주소가 인출을 요청한 본인 지갑과 다르면, 경고에 그치지 않고 인출 자체를 막습니다.

#### Scope (적용 범위)

Aave 스테이킹(Safety Module·sGHO·Umbrella)의 인출에 적용됩니다. 받는 주소가 인출을 요청한 본인 지갑 주소와 다른 경우 발동합니다. (Redeem)

#### Audience (대상 사용자)

Aave Safety Module(stkAAVE)·sGHO·Umbrella에 자산을 맡기고 직접 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

스테이킹 venue 이름(`context.venue.name`), 자산을 받을 주소(`context.recipient`), 인출을 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-redeem-recipient-self")
@severity("deny")
@reason("이 Aave redeem 이 인출한 자산을 본인 지갑이 아닌 주소로 보냅니다 — 차단했습니다")
forbid (
    principal,
    action == Staking::Action::"Redeem",
    resource
)
when
{
    ["aave_safety_module",
     "aave_savings_gho",
     "aave_umbrella_stake_token"].contains
        (
            context.venue.name
        ) &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-redeem-recipient-self",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "staking" },
      "action.tag": { "eq": "redeem" }
    }
  }
}
```
{% endcode %}

***

**STAKE-004: Aave 스테이킹 자산을 본인이 아닌 주소로 인출할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
