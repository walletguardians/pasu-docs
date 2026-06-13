---
description: Granting Morpho Position Authorization to a Flagged Operator (DENY)
---

# LEND-002: Morpho에서 악성으로 분류된 주소에 포지션 관리 권한을 위임할 시 차단

### Policy Definition (정책 정의)

> Morpho에서 다른 주소에 내 포지션을 관리할 권한을 넘기려 할 때, **그 주소가 악성으로 분류된 경우** 위임을 **차단**합니다.

Morpho의 `setAuthorization`은 다른 주소를 내 포지션 관리자(operator)로 지정해, 그 주소가 내 대신 담보를 옮기거나 대출을 일으키도록 허용하는 기능입니다. 권한을 한 번 넘기면 그 operator는 내 서명 없이도 포지션을 건드릴 수 있어, 상대를 잘못 고르면 담보가 통째로 빠져나갈 수 있습니다. 권한을 받으려는 주소가 악성으로 분류돼 있으면, 경고에 그치지 않고 위임 자체를 막습니다.

#### Scope (적용 범위)

Morpho에서 포지션 관리 권한을 위임하는 경우에 적용됩니다. 권한을 새로 넘길 때(`isAuthorized == true`)만 발동하며, 기존 권한을 거두는 동작은 대상이 아닙니다. (Set Authorization)

#### Audience (대상 사용자)

Morpho에서 자동화 봇이나 다른 주소에 포지션 관리를 맡기는 사용자

#### Used Data (판정에 사용될 데이터)

권한을 받으려는 주소(`action.authorized`)의 악성 분류 여부(`authorizedFlagged` — 서버 조회 결과)와, 그 동작이 권한을 새로 넘기는 것인지(`context.isAuthorized`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("set-authorization-operator-reputation-deny")
@severity("deny")
@reason("Morpho에서 권한을 주려는 주소가 악성으로 분류됐습니다 — 차단했습니다")
forbid(principal, action == Lending::Action::"SetAuthorization", resource)
when {
  context.isAuthorized == true
  && context has custom
  && context.custom has authorizedFlagged
  && context.custom.authorizedFlagged == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "set-authorization-operator-reputation-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "set_authorization" } } },
  "policy_rpc": [{
    "id": "authorized-rep",
    "method": "address.reputation",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.authorized" },
    "outputs": [{ "kind": "context", "field": "authorizedFlagged", "type": "Bool", "from": "$.result.flagged", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "authorizedFlagged": "Bool" } }
}
```
{% endcode %}

***

**LEND-002: Morpho에서 악성으로 분류된 주소에 포지션 관리 권한을 위임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
