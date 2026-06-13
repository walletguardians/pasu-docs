---
description: Launchpad Allocation Sent to a Third Party (WARN)
---

# LAUNCH-003: 배정 토큰 클레임 수령처가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고

### Policy Definition (정책 정의)

> 배정받은 토큰을 클레임할 때, 수령처가 본인 지갑도 아니고 허용된 주소도 아닌 다른 주소로 지정한 경우 경고합니다.

Launchpad 세일에 참여하면(commit) 참여 금액만큼 토큰이 특정 지갑 앞으로 배정됩니다. 배정 토큰은 한 번 엉뚱한 곳으로 가면 되돌리기 어렵습니다. 따라서 클레임 수령처가 서명하는 본인 지갑과 다르고 승인 목록에도 없는 경우 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

배정 토큰 클레임 수령처가 승인 목록에 없는 경우 적용됩니다.

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 배정 토큰을 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

클레임 토큰을 받는 주소(`context.recipient`)가 서명하는 지갑 주소(`principal.address`)와 같거나 미리 등록해 둔 승인 목록에 포함되는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-allocation-recipient-not-self-warn")
@severity("warn")
@reason("배정 토큰 클레임 수령처가 승인 목록에 없는 주소입니다 — 수령 주소를 확인하세요")
forbid(principal, action == Launchpad::Action::"ClaimAllocation", resource)
when {
    context.recipient != principal.address &&
    !["0x1111111111111111111111111111111111111111"].contains
        (
            context.recipient
        )
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

**LAUNCH-003:** 배정 토큰 클레임 수령처가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
