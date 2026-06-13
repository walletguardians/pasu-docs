---
description: Bridge via a Protocols Not on the Allowlist (WARN)
---

# BRIDGE-008: 허용되지 않은 프로토콜을 사용해 브리지할 시 경고

### Policy Definition (정책 정의)

> 미리 허용해 둔 목록에 없는 **브리지  프로토콜**로 자산을 옮기려 하면, 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 이 과정은 어느 프로토콜을 사용하느냐에 전적으로 달려 있어서, 잘 검증된 프로토콜 대신 낯선 프로토콜을 사용하면 자산이 도착 체인에서 풀리지 않거나 그대로 묶일 수 있습니다. 그래서 신뢰하는 프로토콜만 목록으로 정해 두고, 그 밖의 프로토콜을 사용하면 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 요청에 적용됩니다. 사용되는 프로토콜이 허용 목록에 없을 때 적용됩니다.

#### Audience (대상 사용자)

여러 체인에 자산을 나눠 두고 특정 브리지 프로토콜만 사용해서 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

이번 전송이 거치는 프로토콜(`context.protocol.name)` 이 허용 목록에 포함되어 있는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-protocol-not-allowlisted-warn")
@severity("warn")
@reason("허용한 브리지 목록에 없는 프로토콜을 사용합니다 — 해당 프로토콜을 선택한게 맞는지 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    !(["across_spoke_pool", "lifi_diamond"].contains(context.protocol.name))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-protocol-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "_template": {
    "allowedProtocols": {
      "cedar_set": "[\"across_spoke_pool\", \"lifi_diamond\"]",
      "default": "across_spoke_pool + lifi_diamond (both supported protocols → dormant by default)",
      "meaning": "bridge protocols the user trusts. With both allowed this never fires. Narrow to e.g. [\"across_spoke_pool\"] to warn on the Li.Fi aggregator route."
    }
  }
}
```
{% endcode %}

***

**BRIDGE-008: 허용되지 않은 브리지 경로를 사용할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
