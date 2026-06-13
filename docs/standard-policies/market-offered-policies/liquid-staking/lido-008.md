---
description: Lido Withdrawal Request Naming a Burn Address as the unstETH Owner (DENY)
---

# LIDO-008: 출금 청구권(unstETH NFT) 소유자를 소각 주소로 지정할 시 차단

### Policy Definition (정책 정의)

> Lido 출금 요청이 unstETH NFT 소유자를 **소각 주소**로 지정하는 경우 차단합니다.

Lido에서 스테이킹한 ETH를 빼려면 먼저 출금을 요청합니다. 이때 소유자로 지정한 주소가 unstETH라는 NFT를 받는데, 이 NFT가 나중에 ETH를 청구할 권리를 담은 영수증입니다. ETH는 이 NFT를 가진 주소만 청구할 수 있습니다. 그런데 NFT 소유자를 소각 주소(0x000…dead)로 지정하면, 청구권이 개인키 없는 주소로 넘어가 누구도 ETH를 꺼낼 수 없습니다. 스테이킹했던 원금이 그대로 묶여 사라지는 셈입니다. 따라서 출금 청구권이 소각 주소로 향하는 요청은 서명하기 전에 차단합니다.

#### Scope (적용 범위)

Lido 출금 요청에 적용됩니다. 요청에 지정된 unstETH NFT 소유자가 소각 주소(0x000…dead)일 때 발동합니다. (RequestWithdrawal)

#### Audience (대상 사용자)

Lido에 스테이킹한 ETH를 출금하려는 사용자

#### Used Data (판정에 사용될 데이터)

출금 요청에 지정된 unstETH NFT 소유자 주소(`context.owner`)와 대상 프로토콜이 Lido인지 여부(`context.venue.name`). 소유자가 소각 주소 0x000…dead와 일치하면 차단합니다. (0x0은 Lido가 요청자 본인으로 바꿔 처리하므로 소각이 아니라 자기 자신으로 보고 제외합니다.)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdrawal-owner-burn-deny")
@severity("deny")
@reason("이 출금 요청이 unstETH NFT 소유자를 소각 주소로 지정합니다 — 청구할 ETH를 영영 되찾을 수 없어 차단했습니다")
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
&#xNAN;_&#x53;upported Chain: Ethereum_
