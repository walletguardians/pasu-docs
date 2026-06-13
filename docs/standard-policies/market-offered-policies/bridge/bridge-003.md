---
description: Bridge to a Non-EVM Raw Address (WARN)
---

# BRIDGE-003: EVM이 아닌 raw 주소로 브리지할 시 경고

### Policy Definition (정책 정의)

> 도착 체인의 **받는 주소가 EVM 형식이 아닌 raw 주소**일 때, 보내기 전에 그 주소가 맞는지 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고, 도착 체인에서 받는 주소로 같은 가치를 풀어주는 방식으로 자산을 옮깁니다. 도착 체인이 이더리움처럼 EVM 계열이면 받는 주소가 익숙한 `0x…` 40자리 주소라 한눈에 알아볼 수 있습니다. 하지만 Solana나 Cosmos처럼 EVM이 아닌 체인으로 보낼 때는, 받는 주소가 32바이트 raw 값으로 들어갑니다. 이런 주소는 형태가 낯설어 한 글자가 틀려도 눈으로 잡아내기 어렵고, 한 번 잘못 보내면 도착 체인에서 자산을 되찾을 길이 없습니다. 따라서 받는 주소가 EVM이 아닌 raw 주소로 지정되면, 주소가 정말 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

크로스체인 브리지의 자산 보내기(Bridge Send)에 적용됩니다. 받는 주소가 EVM 주소가 아니라 32바이트 raw 형식으로 들어간 경우에만 발동합니다.

#### Audience (대상 사용자)

EVM 체인에서 Solana·Cosmos 등 다른 계열 체인으로 자산을 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

도착 체인 받는 주소의 형식 (`context.dstRecipient.kind` — 디코드한 브리지 주문에서 읽은 주소 종류. `raw`이면 EVM이 아닌 주소)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-recipient-raw-nonevm-warn")
@severity("warn")
@reason("EVM이 아닌(32바이트 raw) 주소로 보냅니다 — 눈으로 확인하기 어려우니 받는 주소를 다시 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when { context.dstRecipient.kind == "raw" };
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
