---
description: Launchpad Allocation Sent to a Third Party (WARN)
---

# LAUNCH-001: 세일 배정 권리 수령처가 본인 지갑이 아닐 시 경고

### Policy Definition (정책 정의)

> 세일에 돈을 넣으면서 배정 권리를 받을 **수령처**를 본인 지갑이 아닌 다른 주소로 지정한 경우 경고합니다.

토큰 세일에 참여(commit)할 때는 돈을 내는 지갑과 그 대가로 배정을 받을 주소를 따로 지정할 수 있습니다. 보통 둘은 같은 본인 지갑이지만, 받는 주소 칸에 다른 주소가 들어가면 내 돈으로 산 배정이 그 주소로 잡힙니다. 세일에 한 번 넣은 돈은 되돌리기 어렵고, 잘못 지정한 수령처가 피싱 사이트가 끼워 넣은 주소거나 오타로 친 주소라면 배정 전체를 그대로 잃게 됩니다. 따라서 받는 주소가 서명하는 본인 지갑과 다르면, 넣기 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

토큰 세일 참여(commit)에 적용됩니다. 배정을 받을 수령처가 서명하는 본인 지갑 주소와 다른 경우에 발동합니다. (Commit)

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 배정을 받는 사용자

#### Used Data (판정에 사용될 데이터)

배정을 받을 수령처 주소(`context.recipient`)와 세일에 참여하며 서명하는 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("commit-recipient-not-self-warn")
@severity("warn")
@reason("세일 배정 권리 수령처가 본인 지갑이 아닙니다 — 넣은 돈에 대한 배정이 다른 주소로 잡힙니다")
forbid(principal, action == Launchpad::Action::"Commit", resource)
when {
  context.recipient != principal.address
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

**LAUNCH-001: 세일 배정 권리 수령처가 본인 지갑이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
