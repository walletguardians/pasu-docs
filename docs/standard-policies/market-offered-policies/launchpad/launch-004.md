---
description: Claiming a Launchpad Allocation That Is Not Yet Claimable (WARN)
---

# LAUNCH-004: 아직 받을 수 없는 배정을 클레임할 시 경고

### Policy Definition (정책 정의)

> 아직 받을 수 없는 상태인 **Launchpad 배정**을 클레임하려는 경우 경고합니다.

토큰 세일(IDO)에 참여하면, 받을 토큰이 정해진 일정에 따라 풀립니다. 보통은 베스팅 기간이 지나거나 클레임 창이 열려야 실제로 받을 수 있는데, 컨트랙트가 아직 그 상태가 아니면 클레임 트랜잭션은 되돌려지거나 0개만 받고 가스만 날립니다. 받을 수 있는 시점을 착각했거나, 가짜 클레임 페이지에 속아 미리 서명하는 경우에 이런 일이 생깁니다. 따라서 배정이 아직 클레임 가능한 상태가 아니면, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Launchpad 배정 클레임에 적용됩니다. 클레임하려는 배정이 아직 받을 수 없는 상태인 경우에만 발동합니다. (Claim Allocation)

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 배정 토큰을 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

배정이 지금 받을 수 있는 상태인지 여부(`context.isClaimable`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-allocation-not-claimable-warn")
@severity("warn")
@reason("아직 받을 수 없는(미배정) 클레임입니다 — 배정 가능 상태를 확인한 뒤 서명하세요")
forbid(principal, action == Launchpad::Action::"ClaimAllocation", resource)
when {
  context.isClaimable == false
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-allocation-not-claimable-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim_allocation" } } }
}
```
{% endcode %}

***

**LAUNCH-004: 아직 받을 수 없는 배정을 클레임할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
