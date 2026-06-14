---
description: ETH Withdrawal Claim Sent to a Burn Address (DENY)
---

# Lido에서 출금 청구 시 ETH를 받는 주소가 소각 주소인 경우 차단

### Policy Definition (정책 정의)

> 출금 청구로 받는 **ETH가 소각 주소로 향하는 경우**, 자금을 영영 되찾을 수 없어 차단합니다.

Lido에서 stETH를 출금 신청하면 unstETH라는 NFT가 발행되고, 나중에 그 NFT를 가진 사람이 출금 청구(Claim)를 해서 ETH를 받습니다. 이때 받는 주소가 소각 주소로 설정되면 출금한 ETH가 그대로 소각됩니다.

이 정책은 출금 ETH의 수령 주소가 소각 주소(0x000…000 또는 0x000…dead)일 때 차단합니다.

#### Scope (적용 범위)

Lido 출금 청구에 적용됩니다.

#### Audience (대상 사용자)

Lido에서 스테이킹을 풀어 ETH를 찾는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금 청구를 하는 프로토콜(`context.protocol.name`)
* ETH를 받을 주소(`context.recipient`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-burn-deny")
@severity("deny")
@reason("소각 주소로 출금됩니다. 받는 주소를 확인하세요")
forbid (
    principal,
    action == LiquidStaking::Action::"ClaimWithdrawal",
    resource
)
when
{
    context.protocol.name == "lido" &&
    context has recipient &&
    ["0x0000000000000000000000000000000000000000",
     "0x000000000000000000000000000000000000dead"].contains
        (
            context.recipient
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-burn-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "claim_withdrawal" }
    }
  },
  "_template": {
    "burnAddresses": { "cedar_set": "[\"0x0000000000000000000000000000000000000000\", \"0x000000000000000000000000000000000000dead\"]", "default": "zero + dead", "meaning": "addresses whose funds are permanently unrecoverable (protocol constants)" }
  }
}
```
{% endcode %}

***

**LIDO-011: 출금 청구한 ETH를 소각 주소로 받을 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
