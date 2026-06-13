---
description: Airdrop Claim Above USD Cap (WARN)
---

# AIRDROP-007: 에어드롭 클레임 금액이 $50,000을 초과할 시 경고

### Policy Definition (정책 정의)

> 받으려는 에어드롭 클레임 금액이 미화 약 $50,000을 넘는 경우, 규모를 다시 확인하도록 경고합니다.

에어드롭 클레임은 배포 컨트랙트에서 내 몫의 토큰을 지갑으로 받아오는 서명입니다. 받는 금액이 클수록, 그 클레임을 가짜 배포처가 미끼로 깔아둔 것이거나 받는 주소가 내 지갑이 아닐 때 한 번에 잃는 금액도 커집니다. 클레임 수량을 시세로 환산한 값이 기준을 넘으면, 배포처와 받는 주소가 맞는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. 받는 토큰과 수량을 시세로 환산했을 때 $50,000을 넘는 경우입니다. (Claim)

#### Audience (대상 사용자)

여러 프로토콜의 에어드롭과 리워드를 챙겨 받는 사용자

#### Used Data (판정에 사용될 데이터)

받는 토큰(`claimToken`)과 수량(`actualAmount`)의 USD 환산 가치 (`claimUsd` — 시세 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-usd-cap-warn")
@severity("warn")
@reason("고액 에어드롭 클레임입니다(약 $50,000 초과) — 배포처와 받는 주소를 확인하세요")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context has custom
  && context.custom has claimUsd
  && context.custom.claimUsd.greaterThan(decimal("50000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } },
  "policy_rpc": [{
    "id": "airdrop-claim-usd",
    "method": "oracle.usd_value",
    "params": {
      "chain_id": "$.root.chain_id",
      "asset": "$.action.claimToken",
      "amount": "$.action.actualAmount"
    },
    "outputs": [{ "kind": "context", "field": "claimUsd", "type": "Decimal", "from": "$.result.usd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "claimUsd": "decimal" } }
}
```
{% endcode %}

***

**AIRDROP-007: 에어드롭 클레임 금액이 $50,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
