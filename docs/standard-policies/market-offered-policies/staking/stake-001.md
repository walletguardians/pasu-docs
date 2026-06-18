# STAKE-001: Aave 스테이킹 지분을 본인이 아닌 주소로 받을 시 차단

### Policy Definition (정책 정의)

> Aave 스테이킹으로 받는 지분(shares)이 **본인 지갑이 아닌** 주소로 발행되는 경우, 스테이킹을 차단합니다.

Aave Safety Module(stkAAVE)이나 Savings GHO, Umbrella에 토큰을 스테이킹하면 예치한 만큼의 지분 토큰이 발행됩니다. 이 지분 토큰이 내 원금의 소유권이 됩니다.

그런데 이 스테이킹 함수할 때 지분을 받을 주소가 본인 지갑으로 설정되지 않으면 원금은 내 지갑에서 빠져나가지만 그 권리는 남의 주소로 넘어갑니다.

이 정책은 지분을 받을 주소가 스테이킹을 요청한 본인 지갑과 다르면 차단합니다.

#### Scope (적용 범위)

Aave Safety Module, Savings GHO, Umbrella 스테이킹에 적용됩니다.

#### Audience (대상 사용자)

stkAAVE, Savings GHO, Umbrella 등 Aave에 직접 토큰을 스테이킹하는 사용자

#### Used Data (판정에 사용될 데이터)

* 스테이킹 대상 컨트랙트(`context.venue.name`)
* 지분을 받을 주소(`context.recipient`)
* 스테이킹을 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-stake-recipient-self")
@severity("deny")
@reason("Aave 스테이킹 지분이 본인 지갑이 아닌 주소로 발행됩니다")
forbid (
    principal,
    action == Staking::Action::"Stake",
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
  "id": "aave-stake-recipient-self",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "staking" },
      "action.tag": { "eq": "stake" }
    }
  }
}
```
{% endcode %}

***

STAKE-001: Aave 스테이킹 지분을 본인이 아닌 주소로 받을 시 차단\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
