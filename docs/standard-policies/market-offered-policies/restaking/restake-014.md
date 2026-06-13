---
description: Undelegating on Behalf of Another Staker (WARN)
---

# RESTAKE-014: 본인이 아닌 다른 스테이커의 위임을 해제할 시 경고

### Policy Definition (정책 정의)

> 위임을 해제하려는 대상이 **지금 서명하는 본인이 아닌 다른 스테이커**일 때, 그 계정의 위임을 풀려는 게 맞는지 한 번 더 확인하도록 경고합니다.

EigenLayer에서 위임 해제(Undelegate)는 스테이커가 오퍼레이터에게 맡겨둔 리스테이크 지분을 거두어들이는 동작입니다. 보통은 내가 내 위임을 푸는 거라 해제 대상 스테이커와 서명자가 같아야 하는데, 이번 요청은 그 둘이 어긋나 다른 사람의 계정을 가리키고 있습니다. 실제 메인넷에서는 이 staker 필드가 호출자 본인으로 강제되는 편이라, 남의 위임을 대신 푸는 동작 자체가 드뭅니다. 그래서 잘못 채워진 파라미터일 가능성도 있고, 의도한 게 맞다 해도 짚고 넘어갈 만합니다. 따라서 해제 대상이 서명자와 다르면 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

EigenLayer에서 위임을 해제하는 경우에 적용됩니다. 해제 대상 스테이커가 지금 트랜잭션을 보내는 주소와 다를 때만 발동합니다. (Undelegate)

#### Audience (대상 사용자)

EigenLayer에 리스테이크하고 오퍼레이터에게 위임해 둔 사용자

#### Used Data (판정에 사용될 데이터)

위임을 해제하려는 대상 스테이커(`context.staker`)와 이번 트랜잭션을 보내는 주소(`context.meta.submitter`). 이 둘이 다른지 비교하며, 별도의 외부 조회 없이 요청 안의 값만으로 판정합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("undelegate-other-staker-warn")
@severity("warn")
@reason("본인이 아닌 다른 스테이커의 위임을 해제합니다 — 그 계정의 위임을 해제하려는 게 맞는지 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"Undelegate",
    resource
)
when { context.staker != context.meta.submitter };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "undelegate-other-staker-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "undelegate" } } }
}
```
{% endcode %}

***

**RESTAKE-014: 본인이 아닌 다른 스테이커의 위임을 해제할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
