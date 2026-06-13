---
description: Unlimited Permit2 Allowance Signature (WARN)
---

# TOKEN-006: 무제한 Permit2 승인 서명 요청 시 경고

### Policy Definition (정책 정의)

> Permit2 승인을 무제한 한도로 서명하려는 경우 경고합니다.

Permit2는 토큰 승인 방식으로, 서명 한 번으로 토큰 사용 권한을 위임합니다. 이때 한도를 최대치(무제한)로 설정하면, 위임받은 상대는 그 권한이 살아 있는 동안 내 토큰 잔액 전부를 언제든 옮길 수 있습니다. 무제한 서명이 감지되면 그 대상이 신뢰할 수 있는 대상인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Permit2 승인 한도가 최대치(uint160 max)로 설정된 경우에 적용됩니다.

#### Audience (대상 사용자)

ERC-20 토큰을 보유중인 모든 사용자

#### Used Data (판정에 사용될 데이터)

Permit2 서명에 담긴 허용 한도(`context.amount`)가 최댓값인지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("permit2-sign-unlimited-warn")
@severity("warn")
@reason("Permit2 허용량이 무제한으로 설정돼 있습니다 - 신뢰할 수 있는 대상인지 확인하세요")
forbid(principal, action == Token::Action::"Permit2SignAllowance", resource)
when {
  context.amount == "0xffffffffffffffffffffffffffffffffffffffff"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "permit2-sign-unlimited-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "permit2_sign_allowance" } } }
}
```
{% endcode %}

***

**TOKEN-006:** 무제한 Permit2 승인 서명 요청 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
