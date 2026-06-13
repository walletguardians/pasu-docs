# STAKE-003: Aave 스테이킹 인출 쿨다운을 시 인출 기간을 확인하도록 경고

### Policy Definition (정책 정의)

> Aave Safety Module(stkAAVE·Umbrella)에서 인출하기 위해 쿨다운(cooldown)을 시작하는 경우, 인출 기간 동안에만 회수할 수 있다는 점을 확인하도록 경고합니다.

Aave Safety Module에 스테이킹한 토큰을 인출하기 위해서는, (1) 먼저 쿨다운을 걸고 정해진 기간을 기다린 뒤, (2) 그 다음에 열리는 짧은 인출 기간 동안에만 회수(redeem)할 수 있습니다. 인출 기간이 끝나면 쿨다운을 처음부터 다시 걸어야 합니다. 본 정책은 서명하기 전에 인출 기간을 한번 더 확인할 수 있도록 한 번 더 확인시킵니다.

#### Scope (적용 범위)

Aave Safety Module(stkAAVE 및 Umbrella stake token)의 인출 쿨다운 시작에 적용됩니다.

#### Audience (대상 사용자)

stkAAVE나 Aave Umbrella에 토큰을 스테이킹하고 인출을 준비하는 사용자

#### Used Data (판정에 사용될 데이터)

쿨다운을 거는 staking 대상(`context.venue.name` — `aave_safety_module` 또는 `aave_umbrella_stake_token`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-cooldown-confirm-warn")
@severity("warn")
@reason("Aave 언스테이크 쿨다운을 시작합니다 — 이후 열리는 인출 기간 동안에만 회수(redeem) 할 수 있으니 확인하세요")
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
\&#xNAN;_Supported Chain: Ethereum_
