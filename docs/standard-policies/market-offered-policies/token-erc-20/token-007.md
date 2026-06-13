---
description: Permit2 Allowance With Far-Future Expiration (WARN)
---

# TOKEN-007: 만료가 1년 넘게 남은 Permit2 승인 요청 시 경고

### Policy Definition (정책 정의)

> Permit2 승인의 만료 시점이 지금으로부터 1년 넘게 남은 경우 경고합니다.

Permit2 승인에는 만료 시점이 있어서, 그 기간 동안에만 상대가 토큰을 옮길 수 있습니다. 만료를 너무 멀리(1년 이상) 잡으면, 그 사이 권한을 위임받은 컨트랙트나 상대가 해킹당했을 때 노출되는 기간도 그만큼 길어집니다. 만료가 과도하게 먼 경우, 기간을 줄여 서명하도록 경고합니다.

#### Scope (적용 범위)

Permit2 승인(Permit2 Approve). 만료 시점이 현재로부터 1년(31,536,000초)을 초과하는 경우에 적용됩니다.

#### Audience (대상 사용자)

Permit2를 사용하는 dApp 이용자

#### Used Data (판정에 사용될 데이터)

Permit2 승인의 만료 시각(`expiresAt`)과 현재 시각(`nowTs`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("permit2-far-expiration-warn")
@severity("warn")
@reason("이 Permit2 승인의 만료가 1년 넘게 남아 있습니다. 만료 기간을 줄여서 서명하세요.")
forbid(principal, action == Token::Action::"Permit2Approve", resource)
when {
  context has custom
  && context.custom has nowTs
  && context.expiresAt > context.custom.nowTs + 31536000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "permit2-far-expiration-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "permit2_approve" } } },
  "policy_rpc": [{
    "id": "now",
    "method": "clock.now",
    "params": {},
    "outputs": [{ "kind": "context", "field": "nowTs", "type": "Long", "from": "$.result.nowTs", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "nowTs": "Long" } }
}
```
{% endcode %}

***

**TOKEN-007: 만료가 1년 넘게 남은 Permit2 승인 요청 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
