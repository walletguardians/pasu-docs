---
description: Token Approval to a Known-Malicious Spender (DENY)
---

# TOKEN-003: 악성으로 알려진 주소에 토큰 사용을 승인할 시 차단

### Policy Definition (정책 정의)

> **악성으로 신고된 주소**가 ERC-20 토큰 사용 권한(Approve)을 요청하는 경우, 서명이 나가지 않도록 **차단**합니다.

토큰을 승인하면 그 대상(Spender)은 허용한 한도 안에서 언제든 내 토큰을 가져갈 수 있습니다. 상대가 이미 자금 탈취로 신고된 주소라면, 승인하는 순간 공격자에게 토큰을 빼갈 권한을 그대로 넘기는 셈입니다. 권한을 내어주는 순간 토큰이 드레인될 수 있으므로 경고에 그치지 않고 서명 자체를 막습니다.

#### Scope (적용 범위)

ERC-20 Approve 서명. 승인 대상(Spender)이 악성 주소로 분류됐는지 조회해, 분류된 경우에 적용됩니다.

#### Audience (대상 사용자)

모든 Wallet 사용자

#### Used Data (판정에 사용될 데이터)

승인 대상(Spender) 주소의 악성 여부 (`spenderFlagged` — 악성 주소 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("approve-spender-reputation-deny")
@severity("deny")
@reason("악성으로 신고된 주소에 토큰 사용 권한을 넘기려 합니다. 자금 탈취 위험이 있어 차단했습니다.")
forbid(principal, action == Token::Action::"Erc20Approve", resource)
when {
  context has custom
  && context.custom has spenderFlagged
  && context.custom.spenderFlagged == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "approve-spender-reputation-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_approve" } } },
  "policy_rpc": [{
    "id": "spender-rep",
    "method": "address.reputation",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.spender" },
    "outputs": [{ "kind": "context", "field": "spenderFlagged", "type": "Bool", "from": "$.result.flagged", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "spenderFlagged": "Bool" } }
}
```
{% endcode %}

***

**TOKEN-003: 악성으로 알려진 주소에 토큰 사용을 승인할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
