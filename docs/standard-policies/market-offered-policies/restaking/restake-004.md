---
description: Deposit into a Strategy Outside the Allowlist (DENY)
---

# RESTAKE-004: EigenLayer에서 허용 목록에 없는 스트래티지에 예치할 시 차단

### Policy Definition (정책 정의)

> EigenLayer에 자산을 예치할 때, **들어가려는 스트래티지(strategy)가 미리 정해둔 허용 목록에 없으면** 예치를 **차단**합니다.

EigenLayer에서 리스테이킹을 하려면 먼저 자산을 스트래티지에 예치해야 합니다. 스트래티지는 어떤 토큰을 어떤 방식으로 맡길지 정하는 창구인데, 컨트랙트마다 담는 자산도 다르고 위험도 제각각입니다. 엉뚱한 스트래티지로 예치가 흘러가면 원하지 않은 자산에 묶이거나, 사칭·악성 컨트랙트에 자금을 넣게 될 수 있습니다. 그래서 이 정책은 내가 직접 정해둔 허용 목록에 든 스트래티지로만 예치를 내보내고, 목록에 없는 곳으로 가는 예치는 막습니다.

#### Scope (적용 범위)

EigenLayer에 자산을 예치(restake)하는 경우에 적용됩니다. 예치가 향하는 스트래티지 주소가 허용 목록에 없을 때 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에서 정해둔 몇 개 스트래티지에만 예치하고, 그 외로 자금이 새는 것을 막고 싶은 사용자

#### Used Data (판정에 사용될 데이터)

예치가 들어가는 스트래티지 주소(`context.strategy`)와, 정책 안에 직접 적어둔 허용 스트래티지 목록. 들어가려는 주소가 이 목록에 없으면 차단합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-strategy-not-allowlisted-deny")
@severity("deny")
@reason("이 EigenLayer 스트래티지가 엄격 허용 목록에 없어 예치를 차단했습니다")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when
{
    !(["0x0000000000000000000000000000000000000000"].contains(context.strategy))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-strategy-not-allowlisted-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } }
}
```
{% endcode %}

***

**RESTAKE-004: EigenLayer에서 허용 목록에 없는 스트래티지에 예치할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
