# Aave 스테이킹 자산을 본인 지갑이 아닌 주소로 인출할 시 차단

### Policy Definition (정책 정의)

> Aave 스테이킹 자산을 인출(Redeem)하면서 받는 주소가 **본인 지갑이 아닌** 경우, 인출이 나가지 않도록 **차단**합니다.

Aave Safety Module(stkAAVE)·sGHO·Umbrella에 묶어둔 원금을 다시 찾는 인출(Redeem)을 수행할 때, 자금을 받을 주소가 조작되면 원금이 엉뚱한 곳으로 들어갈 수 있습니다. 받는 주소 칸이 조작된 피싱에 당하면 원금을 통째로 잃을 수 있습니다.

이 정책은 인출(Redeem)할 때 스테이킹 자산을 받는 주소가 본인 지갑과 다르면 차단합니다.

#### Scope (적용 범위)

Aave 스테이킹(Safety Module·sGHO·Umbrella)에서 인출(redeem)하는 과정에 발동합니다.

#### Audience (대상 사용자)

Aave Safety Module(stkAAVE)·sGHO·Umbrella에 자산을 맡기고 직접 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

* 스테이킹 venue 이름(`context.venue.name`)
* 자산을 받을 주소(`context.recipient`)
* 인출을 요청한 본인 지갑 주소(`principal.address`)

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
\&#xNAN;_Supported Chain: Ethereum_
