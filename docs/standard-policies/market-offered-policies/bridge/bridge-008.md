---
description: Bridge via a Venue Not on the Allowlist (WARN)
---

# BRIDGE-008: 허용되지 않은 브리지 경로를 사용할 시 경고

### Policy Definition (정책 정의)

> 미리 허용해 둔 목록에 없는 **브리지 경로**로 자산을 옮기려 하면, 그 경로를 신뢰하는지 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인에서 같은 값을 풀어주는 방식으로 자산을 옮깁니다. 이 과정은 어느 컨트랙트를 거치느냐에 전적으로 달려 있어서, 잘 검증된 경로 대신 낯선 컨트랙트를 타면 자산이 도착 체인에서 풀리지 않거나 그대로 묶일 수 있습니다. 그래서 신뢰하는 브리지 경로만 목록으로 정해 두고, 그 밖의 경로로 자산을 보내려 하면 정말 이 브리지를 거쳐도 되는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

크로스체인 브리지 전송에 적용됩니다. 거치는 브리지 경로가 허용 목록(기본값: Across·Li.Fi)에 없을 때 발동합니다. (Bridge Send)

#### Audience (대상 사용자)

특정 브리지만 골라 쓰고, 그 밖의 경로는 거르고 싶은 사용자

#### Used Data (판정에 사용될 데이터)

이번 전송이 거치는 브리지 경로의 이름 (`context.venue.name` — calldata에서 디코드)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-venue-not-allowlisted-warn")
@severity("warn")
@reason("허용한 브리지 목록에 없는 경로를 사용합니다 — 이 브리지를 신뢰하는지 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    !(["across_spoke_pool", "lifi_diamond"].contains(context.venue.name))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-venue-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "_template": {
    "allowedVenues": {
      "cedar_set": "[\"across_spoke_pool\", \"lifi_diamond\"]",
      "default": "across_spoke_pool + lifi_diamond (both supported venues → dormant by default)",
      "meaning": "bridge venues the user trusts. With both allowed this never fires. Narrow to e.g. [\"across_spoke_pool\"] to warn on the Li.Fi aggregator route."
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
