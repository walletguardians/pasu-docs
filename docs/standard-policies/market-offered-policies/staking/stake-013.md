---
description: Ethena sUSDe Stake Crediting Shares to an Address Other Than Yourself (DENY)
---

# STAKE-013: sUSDe 예치 지분을 본인이 아닌 주소로 받을 시 차단

### Policy Definition (정책 정의)

> Ethena에 USDe를 예치하면서 그 대가로 받는 sUSDe 지분(shares)이 **본인 지갑이 아닌** 주소로 가는 경우, 예치를 차단합니다.

Ethena 스테이킹은 USDe를 맡기고 그 대가로 sUSDe 지분을 받는 동작입니다. 이 sUSDe가 곧 예치한 자산에 대한 소유권 증서라, 나중에 원금과 이자를 되찾을 권리도 sUSDe를 들고 있는 주소에 붙습니다. 그런데 예치 함수는 지분을 받을 주소를 따로 지정할 수 있어, 이 칸이 본인이 아닌 주소로 채워지면 내 돈으로 예치했는데 권리는 남에게 넘어갑니다. dApp이 미리 채워준 주소를 그대로 서명하거나 받는 주소 칸이 바꿔치기된 피싱에 걸리면, 예치한 자산을 통째로 잃습니다. 지분을 받을 주소가 예치를 요청한 본인 지갑과 다르면, 서명되기 전에 차단합니다.

#### Scope (적용 범위)

Ethena sUSDe 예치에 적용됩니다. 받을 지분(sUSDe)의 수령 주소가 예치를 요청한 본인 지갑 주소와 다른 경우 발동합니다. (Stake)

#### Audience (대상 사용자)

USDe를 Ethena에 예치해 sUSDe로 굴리는 사용자

#### Used Data (판정에 사용될 데이터)

예치하는 대상이 Ethena sUSDe 금고인지(`context.venue.name`), 지분을 받을 주소(`context.recipient`)와 예치를 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ethena-stake-recipient-self")
@severity("deny")
@reason("이 sUSDe 예치가 발행 지분(shares)을 본인 지갑이 아닌 주소로 보냅니다 — 차단했습니다")
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
&#xNAN;_&#x53;upported Chain: Ethereum_
