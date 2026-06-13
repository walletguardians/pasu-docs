---
description: Redelegating to a Sanctioned Operator (DENY)
---

# RESTAKE-010: 제재 목록에 오른 오퍼레이터에게 재위임할 시 차단

### Policy Definition (정책 정의)

> 재위임하려는 새 오퍼레이터가 제재 목록(OFAC·EU·UN)에 올라 있으면, 재위임을 **차단**합니다.

EigenLayer에서 예치한 지분은 오퍼레이터(operator)에게 위임해 운용을 맡깁니다. 재위임은 지금 위임 중인 오퍼레이터에게서 지분을 떼어 다른 오퍼레이터에게 새로 맡기는 동작인데, 위임을 받은 오퍼레이터는 내 지분에 대해 슬래싱 권한을 쥡니다. 새로 맡길 오퍼레이터가 제재 목록에 올라 있으면 그쪽으로 지분을 옮기는 것 자체가 법적 책임을 부를 수 있고, 한 번 위임이 넘어가면 그 오퍼레이터의 행동에 내 지분이 묶입니다. 그래서 새 오퍼레이터가 제재 목록에 등재돼 있으면 경고에 그치지 않고 재위임 자체를 막습니다.

#### Scope (적용 범위)

EigenLayer에서 지분을 다른 오퍼레이터에게 재위임하는 경우에 적용됩니다. 새로 위임받을 오퍼레이터(`newOperator`)가 제재 목록에 등재된 경우에 발동합니다. (Redelegate)

#### Audience (대상 사용자)

EigenLayer에 예치한 지분을 다른 오퍼레이터로 갈아타려는 리스테이커

#### Used Data (판정에 사용될 데이터)

새로 위임받을 오퍼레이터 주소(`action.newOperator`)의 제재 목록 등재 여부(`newOperatorSanctioned` — 제재 목록 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("redelegate-new-operator-sanctioned-deny")
@severity("deny")
@reason("재위임할 새 EigenLayer 오퍼레이터가 제재 목록(OFAC·EU·UN)에 올라 있어 재위임을 차단했습니다")
forbid (
    principal,
    action == Restaking::Action::"Redelegate",
    resource
)
when
{
    context has custom &&
    context.custom has newOperatorSanctioned &&
    context.custom.newOperatorSanctioned == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "redelegate-new-operator-sanctioned-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "redelegate" } } },
  "policy_rpc": [{
    "id": "new-operator-sanctions",
    "method": "address.sanctions",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.newOperator" },
    "outputs": [{ "kind": "context", "field": "newOperatorSanctioned", "type": "Bool", "from": "$.result.sanctioned", "required": true }],
    "optional": false
  }],
  "custom_context": { "fields": { "newOperatorSanctioned": "Bool" } }
}
```
{% endcode %}

***

**RESTAKE-010: 제재 목록에 오른 오퍼레이터에게 재위임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
