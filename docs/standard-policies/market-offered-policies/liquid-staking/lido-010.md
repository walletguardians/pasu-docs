# Lido에서 출금 청구 시 ETH를 받는 주소가 본인이 아닌 경우 차단

### Policy Definition (정책 정의)

> Lido 출금 청구로 받는 ETH가 **본인 지갑이 아닌 주소**로 가는 경우 차단합니다.

Lido에서 stETH를 출금 신청하면 unstETH라는 NFT가 발행되고, 나중에 그 NFT를 가진 사람이 출금 청구(Claim)를 해서 ETH를 받습니다. 이때 받는 주소를 본인이 아닌 다른 주소로 지정하면, 출금한 ETH가 그대로 그 주소로 빠져나갑니다.&#x20;

이 정책은 청구 시점에 받는 주소가 청구하는 본인 지갑과 다르면 차단합니다.

#### Scope (적용 범위)

Lido 출금 청구(ClaimWithdrawal)에 적용됩니다

#### Audience (대상 사용자)

Lido에 ETH를 스테이킹하고 출금을 청구하는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금 청구에서 ETH를 받을 주소(`context.recipient`)
* 청구하는 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-not-self-warn")
@severity("deny")
@reason("본인 지갑이 아닌 주소로 출금됩니다. 받는 주소를 확인하세요")
forbid (
    principal,
    action == LiquidStaking::Action::"ClaimWithdrawal",
    resource
)
when
{
    context.venue.name == "lido" &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "claim_withdrawal" }
    }
  }
}
```
{% endcode %}

***

**LIDO-010: Lido 출금 ETH를 본인이 아닌 주소로 청구할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
