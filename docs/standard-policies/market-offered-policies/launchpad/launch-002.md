---
description: Launchpad Commit Above USD Cap (WARN)
---

# LAUNCH-002: 세일 참여(commit) 금액이 $50,000을 초과할 시 경고

### Policy Definition (정책 정의)

> 한 번의 **세일 참여(commit)**로 보내는 금액이 미화 약 $50,000을 넘는 경우, 규모를 다시 확인하도록 경고합니다.

토큰 세일(IDO) 참여는 정해진 토큰을 컨트랙트에 보내고 그만큼 새 토큰 배정을 예약하는 일입니다. 보낸 돈은 세일이 끝나거나 배정이 확정될 때까지 묶이고, 프로젝트가 약속을 어기거나 컨트랙트에 문제가 생기면 그대로 돌려받지 못할 수 있습니다. 그래서 한 번에 보내는 금액이 클수록 잃을 수 있는 돈도 커집니다. 참여 금액을 시세로 환산한 값이 기준을 넘으면, 정말 이만큼 넣을지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

Launchpad 세일 참여(commit). 보내는 토큰과 수량을 시세로 환산했을 때 $50,000을 넘는 경우에 적용됩니다. (Commit)

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여하는 사용자

#### Used Data (판정에 사용될 데이터)

참여로 보내는 토큰과 수량을 시세로 환산한 USD 가치 (`commitUsd` — 시세 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("commit-usd-cap-warn")
@severity("warn")
@reason("USD 환산 고액 commit(약 $50k 초과)입니다 — 금액을 확인한 뒤 서명하세요")
forbid(principal, action == Launchpad::Action::"Commit", resource)
when {
  context has custom
  && context.custom has commitUsd
  && context.custom.commitUsd.greaterThan(decimal("50000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "commit-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "commit" } } },
  "policy_rpc": [{
    "id": "commit-usd",
    "method": "oracle.usd_value",
    "params": {
      "chain_id": "$.root.chain_id",
      "asset": "$.action.payToken",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "commitUsd", "type": "Decimal", "from": "$.result.usd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "commitUsd": "decimal" } }
}
```
{% endcode %}

***

**LAUNCH-002: 세일 참여(commit) 금액이 $50,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
