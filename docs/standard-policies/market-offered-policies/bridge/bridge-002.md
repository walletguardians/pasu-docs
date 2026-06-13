---
description: Bridge Recipient is a Burn Address (DENY)
---

# BRIDGE-002: 브리지 수령처가 소각 주소일 시 차단

### Policy Definition (정책 정의)

> 브리지로 보낸 자산을 받을 **도착 체인의 수령처**가 소각 주소이면, 전송을 막아 차단합니다.

브리지는 출발 체인에서 자산을 잠그고, 도착 체인에서 받는 주소(`dstRecipient`)로 같은 가치를 풀어주는 식으로 작동합니다. 그 받는 주소가 소각 주소(0x000…000 또는 0x000…dead)면, 도착 체인에서 풀려난 자산은 아무도 꺼낼 수 없는 곳에 떨어져 그대로 사라집니다. 일반 토큰 전송과 달리 브리지는 출발 자산을 이미 잠근 뒤라 되돌릴 길도 없습니다. 따라서 도착 수령처가 소각 주소면 보낸 자산을 영영 되찾을 수 없으므로, 경고가 아니라 전송 자체를 차단합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 요청에 적용됩니다. (Bridge Send) 도착 수령처가 EVM 주소이고, 그 주소가 소각 주소(0x000…000 또는 0x000…dead)인 경우 차단합니다.

#### Audience (대상 사용자)

Across·Li.Fi 같은 브리지로 체인 간 자산을 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

도착 수령처 주소 (`context.dstRecipient.address`)와 그 주소 형식 (`context.dstRecipient.kind` — EVM 여부). 두 값 모두 브리지 요청을 정적으로 디코드해 얻으며, 받는 주소가 소각 주소 목록에 들어가는지만 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-recipient-burn-deny")
@severity("deny")
@reason("도착 체인의 소각 주소로 자산이 전달됩니다 — 보낸 자산을 영영 되찾을 수 없어 차단했습니다")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    context.dstRecipient.kind == "evm" &&
    context.dstRecipient has address &&
    ["0x0000000000000000000000000000000000000000",
     "0x000000000000000000000000000000000000dead"].contains
        (
            context.dstRecipient.address
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-recipient-burn-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "_template": {
    "burnAddresses": {
      "cedar_set": "[\"0x0000000000000000000000000000000000000000\", \"0x000000000000000000000000000000000000dead\"]",
      "default": "zero + 0x…dead",
      "meaning": "destination recipients whose delivered funds are permanently unspendable. Unlike a Lido withdrawal `owner` (where 0x0 normalizes to msg.sender), a bridge `dstRecipient` of 0x0 means funds are delivered to the zero address on the destination chain = lost, so 0x0 IS a burn here."
    }
  }
}
```
{% endcode %}

***

**BRIDGE-002: 브리지 수령처가 소각 주소일 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
