---
description: Launchpad Allocation Sent to a Third Party (WARN)
---

# LAUNCH-001: 세일 배정 권리를 받을 주소가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고

### Policy Definition (정책 정의)

> 세일에 돈을 넣으면서  클레임 권리를 받을 **수령처**를 본인 지갑도 아니고 허용된 주소도 아닌 다른 주소로 지정한 경우 경고합니다.

토큰 세일에 참여(commit)할 때는 돈을 내는 지갑과 보상을 클레임할 주소를 따로 지정할 수 있습니다. 잘못된 클레임 주소를 등록한다면 배정 전체를 그대로 잃게 되므로 클레임 권리를 받을 주소가 서명하는 본인 지갑과 다르고 승인 목록에도 없는 경우, 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

세일 배정을 받을 주소가 승인 목록에 없는 경우 적용됩니다.

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 배정을 받는 사용자

#### Used Data (판정에 사용될 데이터)

배정을 받을 수령처 주소(`context.recipient`)가 서명하는 지갑 주소(`principal.address`)와 같거나 미리 등록해 둔 승인 목록에 포함되는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("commit-recipient-not-self-warn")
@severity("warn")
@reason("세일 배정 권리 수령처가 승인 목록에 없는 주소입니다 — 수령 주소를 확인하세요")
forbid(principal, action == Launchpad::Action::"Commit", resource)
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
  "id": "commit-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "commit" } } }
}
```
{% endcode %}

***

**LAUNCH-001:** 세일 배정 권리를 받을 주소가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
