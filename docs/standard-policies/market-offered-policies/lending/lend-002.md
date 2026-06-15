# LEND-002: Morpho에서 악성으로 분류된 주소에 포지션 관리 권한을 위임할 시 차단

### Policy Definition (정책 정의)

> Morpho에서 다른 주소에 내 포지션을 관리할 권한을 넘기려 할 때, **그 주소가 악성으로 분류된 경우** 위임을 **차단**합니다.

Morpho에서는 `setAuthorization`으로 특정 주소에게 내 포지션을 대신 관리할 권한을 줄 수 있습니다. 권한을 받은 주소는 내 담보를 빼거나(withdraw), 내 이름으로 대출을 일으키거나, 포지션을 다른 주소로 옮길 수 있습니다. 그래서 위임 대상이 잘못된 주소거나 악성 컨트랙트면, 한 번의 위임만으로 담보가 통째로 빠져나갈 수 있습니다. 권한을 받으려는 주소가 악성으로 분류돼 있으면 위임 자체를 막습니다.

#### Scope (적용 범위)

Morpho에서 포지션 관리 권한을 위임하는 경우에 적용됩니다. 권한을 새로 주는 위임(grant)에만 적용되고, 기존 권한을 없애는 해제(revoke)에는 발동하지 않습니다.

#### Audience (대상 사용자)

Morpho에서 자동화 봇이나 다른 주소에 포지션 관리를 맡기는 사용자

#### Used Data (판정에 사용될 데이터)

* 권한을 받으려는 주소의 악성 분류 여부 (blacklist)
* 권한을 새로 주는 위임인지 (boolean)

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
\&#xNAN;_Supported Chain: Ethereum_
