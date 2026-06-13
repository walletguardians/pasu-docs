---
description: Bridge with an Attached Execution Message (WARN)
---

# BRIDGE-005: Callback이 포함된 브리지일 시 경고

### Policy Definition (정책 정의)

> 도착 체인에서 자산을 받은 뒤 **임의의 컨트랙트 호출(Callback)**&#xAE4C;지 함께 실행하도록 메시지가 첨부된 브리지에 경고를 표시합니다.

브리지는 자산만 보내는 게 아니라, 도착 체인에서 곧바로 실행할 호출(Callback)을 같이 실어 보낼 수 있습니다. 문제는 이 Callback이 무슨 호출을 하는지 지갑 화면에서는 잘 드러나지 않는다는 점입니다. 그래서 브리지에 Callback이 붙어 있으면 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

도착 체인에서 실행될 메시지가 함께 담겨 있을 때 적용됩니다.

#### Audience (대상 사용자)

여러 체인에 자산을 나눠 두고 브리지로 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

브리지 calldata에 도착 체인에서 실행할 메시지(`context.hasMessage`)가 포함 되었는지 확인합니다.

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
