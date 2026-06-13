---
description: stETH Share Transfer to a Burn Address (DENY)
---

# LIDO-006: stETH 지분을 소각 주소로 전송할 시 차단

### Policy Definition (정책 정의)

> stETH 지분(TransferShares)을 **소각 주소**로 보내려는 경우 차단합니다.

stETH는 보유량이 매일 늘어나는 리베이스 토큰이라, 잔액 대신 내부 지분(share) 단위로도 옮길 수 있습니다. 이 지분을 소각 주소(0x000…000 / 0x…dead)로 보내면 잔액을 보낸 것과 결과가 똑같습니다 — 그 주소는 개인키가 없어 누구도 자산을 다시 꺼낼 수 없으니, 보낸 stETH는 그대로 사라집니다. 그래서 받는 주소가 소각 주소면 서명 전에 차단합니다.

#### Scope (적용 범위)

Lido stETH의 지분 전송에 적용됩니다. 받는 주소가 0x000…000 또는 0x…dead일 때 발동합니다. (TransferShares)

#### Audience (대상 사용자)

stETH를 보유하고 지분 단위로 전송하는 Lido 사용자

#### Used Data (판정에 사용될 데이터)

지분을 받을 주소(`context.recipient`)와 거래가 일어나는 venue(`context.venue.name`). 받을 주소가 소각 주소 목록과 일치하는지 봅니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-shares-burn-recipient-deny")
@severity("deny")
@reason("이 stETH 전송이 소각 주소(0x000…000 / 0x…dead)로 갑니다 — 보낸 자금을 영영 되찾을 수 없어 차단했습니다")
forbid (
    principal,
    action == LiquidStaking::Action::"TransferShares",
    resource
)
when
{
    context.venue.name == "lido" &&
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
  "id": "transfer-shares-burn-recipient-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "transfer_shares" }
    }
  },
  "_template": {
    "burnAddresses": { "cedar_set": "[\"0x0000000000000000000000000000000000000000\", \"0x000000000000000000000000000000000000dead\"]", "default": "zero + dead", "meaning": "addresses whose funds are permanently unrecoverable (protocol constants)" }
  }
}
```
{% endcode %}

***

**LIDO-006: stETH 지분을 소각 주소로 전송할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
