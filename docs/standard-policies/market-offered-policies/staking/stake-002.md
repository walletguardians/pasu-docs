---
description: Staking into Aave's Slashing-Backed Safety Module (WARN)
---

# STAKE-002: Aave Safety Module에 원금 슬래싱을 감수하고 스테이킹할 시 경고

### Policy Definition (정책 정의)

> Aave **Safety Module·Umbrella**에 스테이킹하는 경우, 예치한 원금이 슬래싱(slashing)으로 깎일 수 있음을 알리고 확인하도록 경고합니다.

Aave Safety Module은 스테이킹 보상을 받지만, 그 대가로 프로토콜에 손실이 났을 때 스테이커가 맡긴 자산으로 그 손실을 메웁니다. 보상만 보고 들어왔다가 원금이 줄어드는 위험을 모르는 경우가 많아, Safety Module이나 Umbrella stake 토큰에 스테이킹하려 하면 서명 전에 이 위험을 짚어줍니다.

#### Scope (적용 범위)

스테이킹 대상이 Aave Safety Module(`aave_safety_module`) 또는 Umbrella stake 토큰(`aave_umbrella_stake_token`)인 경우에 적용됩니다. (Stake)

#### Audience (대상 사용자)

stkAAVE 등 Aave Safety Module·Umbrella에 자산을 맡기고 스테이킹 보상을 노리는 사용자

#### Used Data (판정에 사용될 데이터)

* 프로토콜 이름(`context.protocol.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-stake-slashing-warn")
@severity("warn")
@reason("이 스테이킹은 원금이 슬래싱 위험에 노출될 수 있습니다 — 감수할지 확인하세요")
forbid (
    principal,
    action == Staking::Action::"Stake",
    resource
)
when
{
    ["aave_safety_module",
     "aave_umbrella_stake_token"].contains
        (
            context.protocol.name
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-stake-slashing-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "staking" },
      "action.tag": { "eq": "stake" }
    }
  }
}
```
{% endcode %}

***

**STAKE-002: Aave Safety Module에 원금 슬래싱을 감수하고 스테이킹할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
