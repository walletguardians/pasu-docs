---
description: Granting Position-Management Authorization on Morpho (WARN)
---

# LEND-001: Morpho 포지션 관리 권한을 위임할 시 경고 (002와 겹쳐서 삭제 예정)

### Policy Definition (정책 정의)

> Morpho에서 다른 주소에게 내 포지션을 관리할 권한을 새로 주는 경우, 위임 대상을 다시 확인하도록 경고합니다.

Morpho에서는 `setAuthorization`으로 특정 주소에게 자신의 포지션을 대신 관리할 권한을 줄 수 있습니다. 권한을 받은 주소는 내 포지션을 대신 조작해서, 담보를 빼거나 자신의 이름으로 대출을 실행하고, 그 자산을 다른 주소로 보낼 수 있습니다. 그래서 위임 대상이 잘못된 주소거나 악성 컨트랙트면, 한 번의 위임만으로 자산이 통째로 빠져나갈 수 있습니다. 새로운 권항 위임이면 정말 이 주소에 포지션 관리를 맡겨도 되는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Morpho `setAuthorization` 서명. 권한을 새로 주는 위임(grant)에만 적용되고, 기존 권한을 없애는 해제(revoke)에는 발동하지 않습니다.

#### Audience (대상 사용자)

Morpho에서 대출·담보 포지션을 운용하며, 봇이나 관리 컨트랙트에 권한을 위임하는 사용자

#### Used Data (판정에 사용될 데이터)

위임이 권한을 주는 것인지 없애는 것인지 나타내는 값(boolean)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("set-authorization-grant-confirm-warn")
@severity("warn")
@reason("Morpho에서 어떤 주소에 권한을 주면 그 주소가 내 포지션을 관리할 수 있습니다 — 위임 대상을 확인하세요")
forbid(principal, action == Lending::Action::"SetAuthorization", resource)
when { context.isAuthorized == true };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "set-authorization-grant-confirm-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "set_authorization" } } }
}
```
{% endcode %}

***

**LEND-001: Morpho 포지션 관리 권한을 위임할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
