# Ethena USDe 지분(sUSDe) 수령자가 본인이 아닌 경우 차단

### Policy Definition (정책 정의)

> Ethena에 USDe를 예치하면서 그 대가로 받는 sUSDe 지분(shares)이 **본인 지갑이 아닌** 주소로 가는 경우, 예치를 차단합니다.

Ethena 스테이킹은 USDe를 맡기고 sUSDe 지분을 받는 동작입니다. 이 sUSDe는 나중에 원금과 이자를 되찾을 권리가 포함된 예치한 자산에 대한 소유권 증서입니다.

지분을 받을 주소가 본인이 아닌 주소인 경우 예치한 자산과 이자를 받을 권리가 남에게 넘어갑니다.

이 정책은 지분을 받을 주소가 예치를 요청한 본인 지갑과 다르면, 서명되기 전에 차단합니다.

#### Scope (적용 범위)

Ethena sUSDe 예치에 적용됩니다.

#### Audience (대상 사용자)

USDe를 Ethena에 예치해 sUSDe로 굴리는 사용자

#### Used Data (판정에 사용될 데이터)

* Ethena에 sUSDe를 예치하는가? (`context.venue.name`)
* 지분을 받을 주소(`context.recipient`)
* 예치를 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ethena-stake-recipient-self")
@severity("deny")
@reason("USDe 스테이킹 지분(sUSDe) 수령자가 내 지갑이 아닙니다")
forbid (
    principal,
    action == Staking::Action::"Stake",
    resource
)
when
{
    context.venue.name == "ethena_staked_usde" &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "ethena-stake-recipient-self",
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

**STAKE-013: sUSDe 예치 지분을 본인이 아닌 주소로 받을 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
