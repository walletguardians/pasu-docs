# 출금 청구권이 소각 주소로 발행될 시 차단

### Policy Definition (정책 정의)

> Lido 출금 요청이 unstETH NFT 소유자를 **소각 주소**로 지정하는 경우 차단합니다.

Lido에서 stETH를 ETH로 빼려면 먼저 출금을 요청하는데, 이때 청구권으로 unstETH라는 NFT가 발행됩니다. 나중에 이 unstETH NFT를 가진 주소가 ETH를 돌려받을 수 있습니다. 즉 unstETH 발행 대상이 소각 주소(`0x000...dead`)로 지정되면 출금이 끝났을 때 ETH가 내 지갑으로는 ETH를 받을 수 없습니다.

#### Scope (적용 범위)

Lido 출금 요청에 적용됩니다.

#### Audience (대상 사용자)

Lido에 스테이킹한 ETH를 출금하려는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금 요청에 지정된 unstETH NFT 소유자 주소(`context.owner`)
* 대상 프로토콜(`context.protocol.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdrawal-owner-burn-deny")
@severity("deny")
@reason("출금 ETH에 대한 청구권인 unstETH NFT이 소각 주소로 발행됩니다")
forbid (
    principal,
    action == LiquidStaking::Action::"RequestWithdrawal",
    resource
)
when
{
    context.venue.name == "lido" &&
    ["0x000000000000000000000000000000000000dead"].contains(context.owner)
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdrawal-owner-burn-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "request_withdrawal" }
    }
  },
  "_template": {
    "burnAddresses": { "cedar_set": "[\"0x000000000000000000000000000000000000dead\"]", "default": "dead only (0x0 EXCLUDED — Lido normalizes _owner==0 to msg.sender, so a zero owner is self, not a burn)", "meaning": "owner addresses whose claim rights are permanently unrecoverable (0x…dead is not normalized; 0x0 is the self-sentinel)" }
  }
}
```
{% endcode %}

***

**LIDO-008: 출금 청구권(unstETH NFT) 소유자를 소각 주소로 지정할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
