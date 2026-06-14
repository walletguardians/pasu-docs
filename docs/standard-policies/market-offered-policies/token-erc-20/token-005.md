---
description: New Approval After Daily Cumulative Cap (WARN)
---

# TOKEN-005: 하루 누적 활동이 한도를 넘은 경우 경고

### Policy Definition (정책 정의)

> &#x20;당일 지갑에서 빠져나간 금액이 한도를 넘은 상태에서 새 승인을 요청하는 경우 경고합니다.

하루 동안 지갑에서 나간 금액이 평소보다 많이 쌓였다는 건, 사용자가 활발하게 거래한다는 뜻일 수도 있지만 누군가 지갑을 터는 신호일 수도 있습니다. 특히 승인은 나중에 자금을 빼갈 권한을 새로 여는 행위라서 이미 당일 활동이 한도를 넘긴 상황에서 새로운 승인을 요청하면 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

지갑의 당일 누적 출금액이  한도를 넘긴 후 새로운 승인에 적용됩니다.

#### Audience (대상 사용자)

ERC-20 토큰을 보유중인 모든 사용자

#### Used Data (판정에 사용될 데이터)

지갑의 당일 누적 출금액(`windowOutflowUsd` )이 한도를 초과하는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("daily-cumulative-approval-cap-warn")
@severity("warn")
@reason("오늘 지갑에서 빠져나간 금액이 이미 한도를 넘었습니다 - 의도된 승인이 맞는지 확인하세요")
forbid(principal, action == Token::Action::"Erc20Approve", resource)
when {
  context has custom
  && context.custom has windowOutflowUsd
  && context.custom.windowOutflowUsd.greaterThan(decimal("10000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "daily-cumulative-approval-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_approve" } } },
  "policy_rpc": [{
    "id": "approval-window",
    "method": "stat_window.snapshot",
    "params": {
      "owner": "$.root.from"
    },
    "outputs": [{ "kind": "context", "field": "windowOutflowUsd", "type": "Decimal", "from": "$.result.windowOutflowUsd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "windowOutflowUsd": "decimal" } }
}
```
{% endcode %}

***

**TOKEN-005:** 하루 누적 활동이 한도를 넘은 경우 경고\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
