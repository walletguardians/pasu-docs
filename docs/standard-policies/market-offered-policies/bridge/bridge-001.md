---
description: Bridge Recipient Is Not Your Own Wallet (WARN)
---

# BRIDGE-001: 브리지 수령처가 본인 지갑이 아닐 시 경고

### Policy Definition (정책 정의)

> 도착 체인에서 자산을 받는 주소가 **본인 지갑이 아닌 경우**, 받는 주소가 맞는지 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 보통은 다른 체인에 있는 내 지갑으로 자산을 옮기는 것이라 받는 주소가 곧 내 주소입니다. 도착 체인의 받는 주소가 서명하는 본인 지갑과 다르면, 정말 그 주소로 보내려는 것이 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 요청에 적용됩니다. 도착 체인의 받는 주소가 EVM 주소이면서 서명하는 본인 지갑 주소와 다른 경우에 적용됩니다.

#### Audience (대상 사용자)

여러 체인에 자산을 나눠 두고 브리지로 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소의 형식(`context.dstRecipient.kind == "evm"`)을 확인하고 도착 체인의 받는 주소 (`context.dstRecipient.address`)와 서명하는 본인 지갑 주소(`principal.address`)가 같은지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-recipient-not-self-warn")
@severity("warn")
@reason("도착 체인에서 자산을 받는 주소가 본인 지갑이 아닙니다 — 받는 주소가 맞는지 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    context.dstRecipient.kind == "evm" &&
    context.dstRecipient has address &&
    context.dstRecipient.address != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  }
}
```
{% endcode %}

***

**BRIDGE-001: 브리지 수령처가 본인 지갑이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
