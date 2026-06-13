---
description: Redelegation to a New Operator Not on the Allowlist (DENY)
---

# RESTAKE-009: EigenLayer에서 허용 목록에 없는 오퍼레이터로 재위임할 시 차단

### Policy Definition (정책 정의)

> EigenLayer에서 리스테이크 지분을 새 오퍼레이터에게 다시 맡길 때, **그 오퍼레이터가 신뢰 목록에 없으면** 재위임을 **차단**합니다.

EigenLayer에서 자산을 리스테이크하면 그 지분을 오퍼레이터(operator)에게 위임하고, 오퍼레이터는 내 지분으로 검증 작업을 맡는 대신 슬래싱 권한까지 쥡니다. 재위임(Redelegate)은 이 위임 대상을 다른 오퍼레이터로 바꾸는 동작이라, 새로 고른 오퍼레이터가 부실하거나 악의적이면 슬래싱으로 지분이 깎이거나 묶일 수 있습니다. 이 정책은 신뢰 목록에 든 오퍼레이터만 통과시키는데, 기본값은 빈 목록(주소 `0x000…000` 하나만 들어 있는 placeholder)이라 신뢰 목록을 채우기 전에는 진짜 오퍼레이터로 옮기는 재위임을 모두 막습니다. 새 오퍼레이터를 쓰려면 먼저 그 주소를 신뢰 목록에 추가하세요.

#### Scope (적용 범위)

EigenLayer에서 위임 대상 오퍼레이터를 바꾸는 경우에 적용됩니다. 옮겨 갈 새 오퍼레이터(`context.newOperator`)가 신뢰 목록에 없을 때만 발동합니다. 기본 신뢰 목록에는 `0x000…000` placeholder만 들어 있어, 그대로 두면 0이 아닌 실제 오퍼레이터는 전부 발동 대상입니다. (Redelegate)

#### Audience (대상 사용자)

EigenLayer에서 직접 고른 오퍼레이터에게만 리스테이크 지분을 맡기려는 사용자

#### Used Data (판정에 사용될 데이터)

옮겨 갈 새 오퍼레이터 주소(`context.newOperator`)가 신뢰 목록에 들어 있는지 여부. 서버 조회 없이 재위임 calldata에서 바로 읽어 목록과 대조합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("redelegate-new-operator-not-allowlisted-deny")
@severity("deny")
@reason("허용 목록에 없는 새 오퍼레이터로 재위임하려 합니다 — 먼저 신뢰 목록에 추가해야 하므로 차단했습니다")
forbid (
    principal,
    action == Restaking::Action::"Redelegate",
    resource
)
when
{
    !(["0x0000000000000000000000000000000000000000"].contains
         (
             context.newOperator
         ))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "redelegate-new-operator-not-allowlisted-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "redelegate" } } }
}
```
{% endcode %}

***

**RESTAKE-009: EigenLayer에서 허용 목록에 없는 오퍼레이터로 재위임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
