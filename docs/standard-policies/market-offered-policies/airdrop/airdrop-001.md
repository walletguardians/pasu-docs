---
description: Airdrop Claim Sent to an Address Other Than Your Own (WARN)
---

# AIRDROP-001: 에어드롭 클레임을 본인 지갑이 아닌 주소로 받을 시 경고

### Policy Definition (정책 정의)

> 클레임한 토큰이 **본인 지갑이 아닌 주소**로 들어가는 경우 경고를 표시합니다.

에어드롭이나 리워드를 클레임할 때는 보통 받을 주소가 서명하는 지갑과 같습니다. 그런데 클레임 트랜잭션 안에 받는 주소를 따로 지정할 수 있는 경우, 그 주소가 내 지갑이 아닌 다른 곳으로 적혀 있으면 애써 클레임한 토큰이 그대로 남의 손에 들어갑니다. 클레임 유도 사이트가 받는 주소만 바꿔치기해 토큰을 빼돌리는 수법이 대표적입니다. 그래서 클레임으로 받을 주소가 서명하는 지갑과 다르면, 정말 그 주소로 보낼 것인지 서명 전에 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. 클레임으로 받을 주소가 서명하는 지갑 주소와 다른 경우입니다. (Claim)

#### Audience (대상 사용자)

여러 프로젝트의 에어드롭과 리워드를 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

클레임한 토큰을 받을 주소(`context.recipient`)와 서명하는 지갑 주소(`principal.address`)를 비교합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-not-self-warn")
@severity("warn")
@reason("이 클레임이 토큰을 본인 지갑이 아닌 주소로 보냅니다 — 받는 주소를 확인하세요")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "claim"
      }
    }
  }
}
```
{% endcode %}

***

**AIRDROP-001: 에어드롭 클레임을 본인 지갑이 아닌 주소로 받을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
