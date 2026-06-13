---
description: Token Claim from a Malicious Distributor Contract (DENY)
---

# AIRDROP-005: 악성으로 분류된 배포 컨트랙트에서 토큰을 클레임할 시 차단

### Policy Definition (정책 정의)

> 에어드롭을 나눠주는 **배포(distributor) 컨트랙트가 악성으로 분류된 경우**, 클레임이 나가지 않도록 차단합니다.

에어드롭 클레임은 프로젝트가 배포해 둔 컨트랙트를 호출해 내 몫의 토큰을 받아오는 일입니다. 그런데 이 컨트랙트가 정상 배포처를 사칭한 가짜이거나 클레임 과정에서 다른 권한까지 가로채도록 짜여 있으면, 토큰을 받기는커녕 지갑이 통째로 털릴 수 있습니다. 받아온 토큰의 출처가 바로 이 컨트랙트라서, 한 번 호출하면 그 안에 심어둔 동작을 막을 방법이 없습니다. 그래서 배포 컨트랙트 주소를 조회한 결과 악성으로 분류되면, 경고에 그치지 않고 클레임 자체를 막습니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. 클레임 대상 컨트랙트가 악성으로 분류된 경우입니다. (Claim)

#### Audience (대상 사용자)

새 에어드롭이 뜰 때마다 클레임 사이트를 직접 찾아 들어가는 사용자

#### Used Data (판정에 사용될 데이터)

클레임 대상 컨트랙트 주소(`$.action.claimTarget.contract`)의 악성 여부 (`distributorFlagged` — 악성 주소 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-distributor-reputation-deny")
@severity("deny")
@reason("이 에어드롭 배포(distributor) 컨트랙트가 악성으로 분류됐습니다 — 클레임을 차단했습니다")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context has custom
  && context.custom has distributorFlagged
  && context.custom.distributorFlagged == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-distributor-reputation-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } },
  "policy_rpc": [{
    "id": "distributor-rep",
    "method": "address.reputation",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.claimTarget.contract" },
    "outputs": [{ "kind": "context", "field": "distributorFlagged", "type": "Bool", "from": "$.result.flagged", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "distributorFlagged": "Bool" } }
}
```
{% endcode %}

***

**AIRDROP-005: 악성으로 분류된 배포 컨트랙트에서 토큰을 클레임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
