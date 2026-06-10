---
description: Unsupported Protocol Signature Request (WARN)
---

# OTHER-001: 지원하지 않는 프로토콜의 서명 요청일 시 경고

### Policy Definition (정책 정의)

> PASU에서 지원하지 않는 행위 또는 프로토콜의 요청에 서명하는 경우 경고를 표시합니다.

PASU 시스템은 지속적인 업데이트가 예정되어 있지만, 현실적인 문제로 인하여 TVL이 작거나 출시된 지 얼마 되지 않은 프로토콜까지는 모두 곧바로 지원하지 못할 수 있습니다. 이 경우, 안전한 거래를 위해 사용자가 직접 거래 대상을 검토하여야 하므로 이 서명은 PASU의 보호 범위 외에 있음을 명시하는 경고를 표시합니다.

#### Scope (적용 범위)

PASU 지원 범위 외

#### Audience (대상 사용자)

PASU 사용자

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("unknown-blind-sign-warn")
@severity("warn")
@reason("이 트랜잭션은 PASU의 해석 범위 밖에 있습니다. 안전한 프로토콜인지 서명 전 검토하세요.")
forbid(principal, action == Core::Action::"Unknown", resource);
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "unknown-blind-sign-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "unknown"
      }
    }
  }
}
```
{% endcode %}

***

**OTHER-001: 지원하지 않는 프로토콜의 서명 요청일 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/09\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
