---
description: Airdrop Claim Sending Tokens to a Burn Address (DENY)
---

# AIRDROP-002: 에어드롭 클레임이 보상을 블랙리스트 주소로 보낼 시 차단

### Policy Definition (정책 정의)

> 에어드롭으로 받을 토큰의 **수령 주소가  블랙리스트에 포함된** 경우, 클레임이 나가지 않도록 **차단**합니다.

에어드롭 클레임은 받을 자격이 있는 보상을 내 지갑으로 옮겨오는 동작입니다. 이때 수령 주소를 소각 주소로 지정하면, 받은 토큰이 곧장 아무도 꺼낼 수 없는 곳으로 들어갑니다. 소각 주소는 개인키가 존재하지 않아 한 번 들어간 자산은 누구도 되찾지 못합니다. 잘못 지정한 주소든 클레임 화면을 조작한 피싱이든, 받는 즉시 토큰을 영영 잃습니다. 그래서 수령 주소가 소각 주소이면 경고에 그치지 않고 클레임 자체를 막습니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임(Claim). 받을 토큰의 수령 주소가 0x000…000 또는 0x000…dead인 경우에 적용됩니다.

#### Audience (대상 사용자)

에어드롭이나 리워드 토큰을 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

클레임한 토큰을 받을 주소 (`context.recipient`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-burn-deny")
@severity("deny")
@reason("이 클레임이 토큰을 소각 주소로 보냅니다 — 받은 토큰을 영영 되찾을 수 없어 차단했습니다")
forbid (
    principal,
    action == Airdrop::Action::"Claim",
    resource
)
when
{
    ["0x0000000000000000000000000000000000000000",
     "0x000000000000000000000000000000000000dead"].contains
        (
            context.recipient
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-burn-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "airdrop" },
      "action.tag": { "eq": "claim" }
    }
  }
}
```
{% endcode %}

***

**AIRDROP-002: 에어드롭 클레임이 보상을 블랙리스트 주소로 보낼 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
