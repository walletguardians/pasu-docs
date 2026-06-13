---
description: Delegating Restaked Stake to a Sanctioned Operator (DENY)
---

# RESTAKE-008: 제재 목록에 오른 오퍼레이터에게 리스테이킹 지분을 위임할 시 차단

### Policy Definition (정책 정의)

> 리스테이킹 지분을 맡기려는 **오퍼레이터가 제재 목록(OFAC·EU·UN)에 오른 경우**, 위임을 **차단**합니다.

EigenLayer에서 위임(DelegateTo)은 내가 예치한 리스테이킹 지분의 운용을 오퍼레이터에게 넘기는 일입니다. 오퍼레이터는 이 지분으로 AVS 검증에 참여하고, 규칙을 어기면 내 지분이 슬래싱으로 깎입니다. 그래서 어떤 오퍼레이터를 고르느냐가 곧 내 자산의 위험을 정합니다. 맡기려는 오퍼레이터가 제재 목록에 올라 있으면 자금세탁·테러자금 등에 연루됐다는 뜻이라, 위임할 경우 법적 책임이 따를 수 있고 슬래싱 위험도 가늠하기 어렵습니다. 따라서 오퍼레이터가 제재 목록에 등재돼 있으면 경고에 그치지 않고 위임 자체를 막습니다.

#### Scope (적용 범위)

EigenLayer에서 오퍼레이터에게 리스테이킹 지분을 위임하는 경우에 적용됩니다. 맡기려는 오퍼레이터 주소가 제재 목록에 등재된 경우에 발동합니다. (Delegate To)

#### Audience (대상 사용자)

EigenLayer에 리스테이크한 지분을 오퍼레이터에게 위임하려는 사용자

#### Used Data (판정에 사용될 데이터)

위임받을 오퍼레이터 주소(`action.operator`)의 제재 목록 등재 여부(`sanctioned` — 제재 목록 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("delegate-operator-sanctioned-deny")
@severity("deny")
@reason("이 리스테이킹 오퍼레이터가 제재 목록(OFAC·EU·UN)에 올라 있어 위임을 차단했습니다")
forbid (
    principal,
    action == Restaking::Action::"DelegateTo",
    resource
)
when
{
    context has custom &&
    context.custom has sanctioned &&
    context.custom.sanctioned == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "delegate-operator-sanctioned-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "delegate_to" } } },
  "policy_rpc": [{
    "id": "operator-sanctions",
    "method": "address.sanctions",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.operator" },
    "outputs": [{ "kind": "context", "field": "sanctioned", "type": "Bool", "from": "$.result.sanctioned", "required": true }],
    "optional": false
  }],
  "custom_context": { "fields": { "sanctioned": "Bool" } }
}
```
{% endcode %}

***

**RESTAKE-008: 제재 목록에 오른 오퍼레이터에게 리스테이킹 지분을 위임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
