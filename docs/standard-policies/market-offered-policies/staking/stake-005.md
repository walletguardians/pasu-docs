---
description: Aave Safety Module Redeem Outside the Unstake Window (WARN)
---

# STAKE-005: Aave Safety Module 스테이킹을 인출 창 밖에서 해제할 시 경고

### Policy Definition (정책 정의)

> **Aave Safety Module**에서 스테이킹을 해제(Redeem)하는데 지금이 인출 창 밖이라면, 쿨다운 시점을 확인하도록 경고합니다.

Aave Safety Module에 stkAAVE처럼 스테이킹한 자산은 곧바로 빼낼 수 없습니다. 먼저 쿨다운(cooldown)을 걸어 정해진 기간을 기다린 뒤, 그 다음에 열리는 짧은 인출 창(unstake window) 안에서만 해제할 수 있습니다. 이 창을 놓치면 redeem이 되돌려지고, 쿨다운을 처음부터 다시 걸어야 합니다. 지갑이 지금 인출 창 안에 있지 않은 상태로 해제를 시도하면, 서명 전에 쿨다운 시점을 다시 확인하도록 알립니다.

#### Scope (적용 범위)

Aave Safety Module의 스테이킹 해제에 적용됩니다. 서버 조회 결과 지금이 인출 창 밖으로 확인된 경우에만 발동합니다. (Redeem)

#### Audience (대상 사용자)

Aave Safety Module(stkAAVE·Umbrella)에 스테이킹하고 직접 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

해제하려는 Safety Module(`context.venue.name`)과, 그 모듈에서 지금이 인출 창 안인지 여부(`context.custom.inUnstakeWindow` — 쿨다운 창 상태 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-redeem-window-warn")
@severity("warn")
@reason("이 Aave Safety Module redeem 이 인출 창 밖이라 되돌려질 수 있습니다 — 쿨다운 시점을 확인하세요")
forbid(principal, action == Staking::Action::"Redeem", resource)
when {
  context.venue.name == "aave_safety_module"
  && context has custom
  && context.custom has inUnstakeWindow
  && !(context.custom.inUnstakeWindow)
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-redeem-window-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "redeem"
      }
    }
  },
  "policy_rpc": [
    {
      "id": "aave-cooldown-window",
      "method": "aave.cooldown_window_state",
      "optional": true,
      "params": {
        "chain_id": "$.root.chain_id",
        "module": "$.action.venue.module",
        "owner": "$.root.from"
      },
      "outputs": [
        {
          "kind": "context",
          "field": "inUnstakeWindow",
          "type": "Bool",
          "from": "$.result.inUnstakeWindow",
          "required": false
        }
      ]
    }
  ],
  "custom_context": {
    "fields": {
      "inUnstakeWindow": "Bool"
    }
  }
}
```
{% endcode %}

***

**STAKE-005: Aave Safety Module 스테이킹을 인출 창 밖에서 해제할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
</content>
</invoke>
