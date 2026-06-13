---
description: ETH Withdrawal Claim Sent to a Burn Address (DENY)
---

# LIDO-011: 출금 청구한 ETH를 소각 주소로 받을 시 차단

### Policy Definition (정책 정의)

> 출금 청구로 받는 **ETH가 소각 주소로 향하는 경우**, 자금을 영영 되찾을 수 없어 차단합니다.

Lido에 스테이킹한 ETH를 빼려면 먼저 출금을 요청해 unstETH NFT를 받고, 출금 대기열(Withdrawal Queue) 처리가 끝나면 그 NFT로 ETH를 청구합니다. 이 청구 단계에서 ETH를 받을 주소를 지정하는데, 그 주소가 소각 주소면 받는 즉시 ETH가 사라집니다. 소각 주소는 개인키(Private Key)를 아무도 모르는 주소라, 한 번 들어간 자금은 누구도 다시 꺼낼 수 없습니다. 따라서 출금 ETH의 수령 주소가 소각 주소(0x000…000 또는 0x000…dead)일 때 이 청구를 차단합니다.

#### Scope (적용 범위)

Lido 출금 청구에 적용됩니다. (Claim Withdrawal — ETH를 받을 주소가 0x000…000 또는 0x000…dead일 때)

#### Audience (대상 사용자)

Lido에서 스테이킹을 풀어 ETH를 찾는 사용자

#### Used Data (판정에 사용될 데이터)

출금 청구를 하는 venue(`context.venue.name`)와 ETH를 받을 주소(`context.recipient`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-burn-deny")
@severity("deny")
@reason("이 출금 청구가 ETH를 소각 주소로 보냅니다 — 자금을 영영 되찾을 수 없어 차단했습니다")
forbid (
    principal,
    action == LiquidStaking::Action::"ClaimWithdrawal",
    resource
)
when
{
    context.venue.name == "lido" &&
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
&#xNAN;_&#x53;upported Chain: Ethereum_
