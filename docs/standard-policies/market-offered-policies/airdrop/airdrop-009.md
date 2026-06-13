---
description: Claim That Is No Longer Claimable (WARN)
---

# AIRDROP-009: 더 받을 것이 없는 클레임을 시도할 시 경고

### Policy Definition (정책 정의)

> 이미 받았거나 기간이 끝나 **더 받을 토큰이 없는** 클레임을 서명하려는 경우 경고를 표시합니다.

에어드롭이나 리워드 클레임은 받을 몫이 남아 있을 때만 토큰이 들어옵니다. 이미 한 번 받았거나 클레임 기간이 끝난 뒤에 다시 서명하면, 토큰은 한 푼도 들어오지 않으면서 가스비만 나갑니다. 받을 게 없는데도 클레임을 유도하는 화면은 가짜 사이트가 서명을 빼내려는 미끼일 때도 많습니다. 그래서 받을 몫이 남아 있지 않은 클레임을 서명하려 하면, 정말 진행할지 미리 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. 받을 몫이 남아 있지 않은 클레임일 때 발동합니다. (Claim)

#### Audience (대상 사용자)

에어드롭이나 스테이킹 리워드를 자주 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

이 클레임에 아직 받을 몫이 남아 있는지 여부 (`isStillClaimable`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-not-claimable-warn")
@severity("warn")
@reason("이 클레임은 더 받을 것이 없습니다(이미 받았거나 만료) — 서명 전에 확인하세요")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context.isStillClaimable == false
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-not-claimable-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } }
}
```
{% endcode %}

***

**AIRDROP-009: 더 받을 것이 없는 클레임을 시도할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
