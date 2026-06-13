---
description: Bridge Recipient is a Burn Address (DENY)
---

# BRIDGE-002: 브리지 수령처가 블랙리스트 주소일 시 차단

### Policy Definition (정책 정의)

> 브리지로 보낸 자산을 받을 **도착 체인의 수령처**가 블랙리스트 주소이면, 전송을 막아 차단합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 도착 체인의 받는 주소가 블랙리스트 주소면 자산을 되찾을 수 없을 수 있으므로 전송 자체를 차단합니다.&#x20;

#### Scope (적용 범위)

도착 체인의 받는 주소가 EVM 주소이면서 그 주소가 설정 블랙리스트 주소인 경우 적용됩니다.

#### Audience (대상 사용자)

여러 체인에 자산을 나눠 두고 브리지로 자산을 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소의 형식(`context.dstRecipient.kind == "evm"`)과 도착 주소(`context.dstRecipient.address`)가 블랙리스트에 들어가있는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-recipient-burn-deny")
@severity("deny")
@reason("도착 체인의 블랙리스트 주소로 자산이 전달됩니다 — 수령 주소를 확인하세요.")
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
