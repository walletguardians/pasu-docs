# 허용 목록에 없는 오퍼레이터에게 재위임할 시 차단

### Policy Definition (정책 정의)

> EigenLayer에서 리스테이크 지분을 새 오퍼레이터에게 다시 맡길 때, **그 오퍼레이터가 신뢰 목록에 없으면** 재위임을 **차단**합니다.

EigenLayer에서 재위임(Redelegate)은 리스테이킹된 자산을 위임할 대상을 다른 오퍼레이터로 바꾸는 동작입니다. 새로 고른 오퍼레이터가 부실하거나 악의적이면 슬래싱을 당할 수 있습니다.

이 정책은 허용 목록에 있는 오퍼레이터만 통과시킵니다.

**! 이 정책을 사용하려면 오퍼레이터 허용 목록을 설정해야 합니다.**

#### Scope (적용 범위)

EigenLayer에서 위임 대상 오퍼레이터를 바꾸는 경우에 적용됩니다.

#### Audience (대상 사용자)

EigenLayer에서 직접 고른 오퍼레이터에게만 리스테이크 지분을 맡기려는 사용자

#### Used Data (판정에 사용될 데이터)

* 옮겨 갈 새 오퍼레이터 주소(`context.newOperator`)

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
\&#xNAN;_Supported Chain: Ethereum_
