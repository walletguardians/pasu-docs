---
description: Starting the Aave Unstake Cooldown (WARN)
---

# STAKE-003: Aave 스테이킹 인출 쿨다운을 시작할 시 경고

### Policy Definition (정책 정의)

> Aave Safety Module(stkAAVE·Umbrella)에서 인출 **쿨다운(cooldown)**을 시작하는 경우, 이후 열리는 인출 창 안에서만 빼낼 수 있다는 점을 확인하도록 경고합니다.

Aave Safety Module에 맡긴 토큰은 바로 빼낼 수 없습니다. 먼저 쿨다운을 걸고 정해진 기간을 기다린 뒤, 그 다음에 열리는 짧은 인출 창(window) 동안에만 redeem이 됩니다. 창이 닫히면 쿨다운을 처음부터 다시 걸어야 하고, 그 사이 프로토콜이 해킹당하면 staking된 원금이 슬래싱으로 깎일 수 있습니다. 쿨다운을 시작하는 트랜잭션은 당장 토큰이 움직이진 않지만 인출 일정의 출발점이라, 서명하기 전에 인출 창 규칙을 알고 누르도록 한 번 더 확인시킵니다.

#### Scope (적용 범위)

Aave Safety Module(stkAAVE 및 Umbrella stake token)의 인출 쿨다운 시작에 적용됩니다. (Cooldown)

#### Audience (대상 사용자)

stkAAVE나 Aave Umbrella에 토큰을 staking하고 인출을 준비하는 사용자

#### Used Data (판정에 사용될 데이터)

쿨다운을 거는 staking 대상(`context.venue.name` — `aave_safety_module` 또는 `aave_umbrella_stake_token`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-cooldown-confirm-warn")
@severity("warn")
@reason("Aave 언스테이크 쿨다운을 시작합니다 — 이후 열리는 인출 창(window) 동안에만 redeem 할 수 있으니 확인하세요")
forbid(principal, action == Staking::Action::"Cooldown", resource)
when {
  ["aave_safety_module", "aave_umbrella_stake_token"].contains(context.venue.name)
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-cooldown-confirm-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "cooldown"
      }
    }
  }
}
```
{% endcode %}

***

**STAKE-003: Aave 스테이킹 인출 쿨다운을 시작할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
