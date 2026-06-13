---
description: Bridge to a Non-EVM Raw Address (WARN)
---

# BRIDGE-003: EVM이 아닌 체인으로 브리지할 시 경고

### Policy Definition (정책 정의)

> 도착 체인의 **받는 주소가 EVM 형식이 아닐** 때, 보내기 전에 그 주소가 맞는지 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 도착 체인이 이더리움처럼 EVM 계열이면 받는 주소가 아니라면 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 요청에 적용됩니다. 도착 수령처가 EVM 주소가 아니라면 적용됩니다.

#### Audience (대상 사용자)

EVM 체인에만 자산을 나눠 두고 브리지로 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소의 형식(`context.dstRecipient.kind != "evm"`)을 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-recipient-raw-nonevm-warn")
@severity("warn")
@reason("EVM이 아닌 주소로 보냅니다 — EVM 체인만 사용하신다면 다시 확인하세요.")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when { context.dstRecipient.kind != "evm" };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-recipient-raw-nonevm-warn",
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

**BRIDGE-003: EVM이 아닌 raw 주소로 브리지할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
