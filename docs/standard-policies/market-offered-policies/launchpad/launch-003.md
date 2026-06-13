---
description: Launchpad Allocation Sent to a Third Party (WARN)
---

# LAUNCH-003: 배정 토큰 클레임 수령처가 본인 지갑이 아닐 시 경고

### Policy Definition (정책 정의)

> 배정받은 토큰을 클레임할 때, 받는 주소가 서명하는 **본인 지갑**이 아니면 경고합니다.

Launchpad 세일에 참여하면(commit) 참여 금액만큼 토큰이 내 지갑 앞으로 배정됩니다. 나중에 이 토큰을 클레임할 때 받는 주소를 따로 지정할 수 있는데, 보통은 서명하는 지갑이 그대로 받는 주소가 됩니다. 그런데 피싱 사이트가 미리 채워둔 클레임이나 손이 미끄러진 입력 탓에, 받는 주소가 남의 지갑으로 바뀐 채 서명되기도 합니다. 배정 토큰은 한 번 엉뚱한 곳으로 가면 되돌리기 어렵습니다. 따라서 클레임 수령처가 서명하는 본인 지갑과 다르면, 서명 전에 받는 주소를 확인하도록 경고합니다.

#### Scope (적용 범위)

Launchpad 배정 토큰 클레임. 받는 주소가 서명하는 지갑 자신과 다른 경우에 적용됩니다. (Claim Allocation)

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 배정 토큰을 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

클레임 토큰을 받는 주소(`context.recipient`)와 서명하는 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-allocation-recipient-not-self-warn")
@severity("warn")
@reason("배정 토큰 클레임 수령처가 본인 지갑이 아닙니다 — 받는 주소를 확인하세요")
forbid(principal, action == Launchpad::Action::"ClaimAllocation", resource)
when {
  context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-allocation-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "claim_allocation"
      }
    }
  }
}
```
{% endcode %}

***

**LAUNCH-003: 배정 토큰 클레임 수령처가 본인 지갑이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
