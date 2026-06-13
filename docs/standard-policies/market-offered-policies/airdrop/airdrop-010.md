---
description: Claim After the Airdrop Window Has Closed (WARN)
---

# AIRDROP-010: 클레임 기간이 종료된 에어드롭을 클레임할 시 경고

### Policy Definition (정책 정의)

> 클레임 기간이 이미 끝난 에어드롭을 클레임하려는 경우, 정상적인 클레임인지 다시 확인하도록 경고합니다.

대부분의 에어드롭은 클레임을 받을 수 있는 기간이 정해져 있고, 그 기간이 지나면 배포 컨트랙트는 더 이상 토큰을 내주지 않습니다. 그런데도 마감된 클레임을 시도하고 있다면, 진짜 배포처가 아니라 끝난 에어드롭을 미끼로 만든 가짜 페이지를 마주했을 가능성이 큽니다. 이런 곳은 클레임을 빌미로 서명이나 승인을 받아내려 합니다. 그래서 클레임 마감 시각이 지금보다 앞서 있으면, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭 토큰 클레임에 적용됩니다. 클레임 마감 시각이 이번 서명 시각보다 이전인 경우에 발동합니다. (Claim)

#### Audience (대상 사용자)

에어드롭 클레임 사이트를 자주 드나드는 사용자

#### Used Data (판정에 사용될 데이터)

이 에어드롭의 클레임 마감 시각(`context.claimWindowEnd`)과 이번 서명 시각(`context.meta.submittedAt`). 마감 시각이 서명 시각보다 앞서면 기간이 끝난 것으로 봅니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-window-closed-warn")
@severity("warn")
@reason("이 에어드롭의 클레임 기간이 이미 끝났습니다 — 이례적인 상태이니 서명 전에 확인하세요")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context has claimWindowEnd
  && context.claimWindowEnd < context.meta.submittedAt
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-window-closed-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } }
}
```
{% endcode %}

***

**AIRDROP-010: 클레임 기간이 종료된 에어드롭을 클레임할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
