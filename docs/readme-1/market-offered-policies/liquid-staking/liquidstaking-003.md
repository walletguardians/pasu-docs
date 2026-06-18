# LIQUID\_STAKING-003: Lido에서 출금 청구권이 본인이 아닌 주소로 발행될 시 차단

### Policy Definition (정책 정의)

> Lido 출금 요청이 청구권(**unstETH NFT**)을 본인 지갑이 아닌 주소로 발행하려는 경우 차단합니다.

Lido에서 stETH를 ETH로 빼려면 먼저 출금을 요청하는데, 이때 청구권으로 unstETH라는 NFT가 발행됩니다. 나중에 이 unstETH NFT를 가진 주소가 ETH를 돌려받을 수 있습니다. 즉 unstETH 발행 대상을 잘못 지정하면 출금이 끝났을 때 ETH가 내 지갑으로는 ETH를 받을 수 없습니다.

이 정책은 청구권의 발행 대상이 본인이 아닌 경우 차단합니다.

#### Scope (적용 범위)

Lido 출금 요청에 적용됩니다.

#### Audience (대상 사용자)

Lido에 stETH를 맡기고 ETH로 출금하려는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금 요청에 담긴 청구권(unstETH) 발행 대상 주소(`context.owner`)
* 서명하는 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdrawal-owner-not-self-warn")
@severity("deny")
@reason("출금 ETH에 대한 청구권인 unstETH NFT이 본인 지갑이 아닌 주소로 발행됩니다")
forbid (
    principal,
    action == LiquidStaking::Action::"RequestWithdrawal",
    resource
)
when
{
    context.venue.name == "lido" &&
    context.owner != principal.address &&
    context.owner != "0x0000000000000000000000000000000000000000"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdrawal-owner-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "request_withdrawal" }
    }
  },
  "_template": {
    "trustedRecipients": { "cedar_extension": "&& !([\"0x...\"].contains(context.owner))", "default": "[] (not applied)", "meaning": "optional allowlist of non-self owners you trust; add to the cedar guard to suppress the warn" }
  }
}
```
{% endcode %}

***

LIQUID\_STAKING-003: Lido에서 출금 청구권이 본인이 아닌 주소로 발행될 시 차단\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
