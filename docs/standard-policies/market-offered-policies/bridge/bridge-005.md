---
description: Bridge with an Attached Execution Message (WARN)
---

# BRIDGE-005: 실행 메시지가 첨부된 브리지일 시 경고

### Policy Definition (정책 정의)

> 도착 체인에서 자산을 받은 뒤 **임의의 컨트랙트 호출**까지 함께 실행하도록 메시지가 첨부된 브리지에 경고를 표시합니다.

Across나 Li.Fi 같은 브리지는 자산만 보내는 게 아니라, 도착 체인에서 곧바로 실행할 호출(call) 한 덩어리를 같이 실어 보낼 수 있습니다. 받은 자산을 바로 스왑하거나 예치하는 식으로 한 번에 처리하려고 쓰는 기능입니다. 문제는 이 메시지가 무슨 호출을 하는지 지갑 화면에서는 잘 드러나지 않는다는 점입니다. dApp이 끼워 넣은 메시지가 도착하자마자 받은 자산을 다른 주소로 빼돌리거나, 의도하지 않은 컨트랙트에 권한을 넘기는 호출일 수도 있습니다. 그래서 브리지에 실행 메시지가 붙어 있으면, 자산만 옮기는 게 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

브리지 전송(Bridge Send)에 적용됩니다. 도착 체인에서 실행될 메시지가 함께 담겨 있을 때 발동합니다.

#### Audience (대상 사용자)

브리지로 받은 자산을 곧바로 스왑·예치까지 묶어 처리하는, 한 번의 서명으로 여러 동작을 실행하는 사용자

#### Used Data (판정에 사용될 데이터)

브리지 calldata에 도착 체인에서 실행할 메시지가 들어 있는지 여부 (`context.hasMessage`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-message-attached-warn")
@severity("warn")
@reason("도착 체인에서 실행될 메시지가 함께 담겼습니다 — 자산 도착 후 임의 호출이 실행되니 내용을 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when { context.hasMessage == true };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-message-attached-warn",
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

**BRIDGE-005: 실행 메시지가 첨부된 브리지일 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
