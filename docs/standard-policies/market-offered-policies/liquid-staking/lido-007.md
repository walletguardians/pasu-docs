---
description: Lido Withdrawal Request with a Non-Self unstETH Owner (WARN)
---

# LIDO-007: 출금 요청의 unstETH NFT를 본인이 아닌 주소로 발행할 시 경고

### Policy Definition (정책 정의)

> Lido 출금 요청이 청구권(**unstETH NFT**)을 본인 지갑이 아닌 주소로 발행하려는 경우 경고합니다.

Lido에서 stETH를 ETH로 빼려면 먼저 출금을 요청하는데, 이때 unstETH라는 NFT가 청구권으로 발행됩니다. 나중에 ETH를 돌려받는 건 이 NFT를 가진 주소뿐입니다. 즉 발행 대상(owner)을 잘못 지정하면 출금이 끝났을 때 ETH가 그 주소로 가고, 내 지갑으로는 한 푼도 돌아오지 않습니다. 그래서 청구권을 본인이 아닌 다른 주소로 발행하려 할 때, 정말 그 주소가 맞는지 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Lido 출금 요청에 적용됩니다. 청구권을 받을 owner가 서명하는 본인 지갑도 아니고 비워 둔 값(0x000…000)도 아닐 때 발동합니다. (RequestWithdrawal)

#### Audience (대상 사용자)

Lido에 stETH를 맡기고 ETH로 출금하려는 사용자

#### Used Data (판정에 사용될 데이터)

출금 요청에 담긴 청구권(unstETH) 발행 대상 주소(`context.owner`)와 서명하는 본인 지갑 주소(`principal.address`)를 비교합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdrawal-owner-not-self-warn")
@severity("warn")
@reason("이 출금 요청이 unstETH NFT를 본인 지갑이 아닌 주소로 발행합니다 — 그 주소만 ETH를 청구할 수 있으니 확인하세요")
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

**LIDO-007: 출금 요청의 unstETH NFT를 본인이 아닌 주소로 발행할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
