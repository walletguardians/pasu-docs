---
description: Airdrop Claim to a Non-Allowlisted Recipient (WARN)
---

# AIRDROP-003: 클레임 수령 주소가 본인 지갑도 승인 목록에도 없을 시 경고

### Policy Definition (정책 정의)

> 에어드롭 토큰을 **본인 지갑도, 미리 승인해 둔 수령 주소 목록에도 없는 주소**로 클레임하는 경우 경고합니다.

에어드롭이나 리워드 클레임은 받을 주소를 따로 지정할 수 있는 경우가 많습니다. 보통은 클레임하는 본인 지갑이 그대로 받지만, 컨트랙트에 따라 받을 주소를 calldata에 직접 넣을 수 있고, 이 주소를 슬쩍 바꿔 토큰을 가로채는 수법이 자주 쓰입니다. 받을 주소가 클레임하는 지갑과 다르고, 본인이 미리 등록해 둔 수령 주소 목록에도 없으면, 의도한 곳으로 가는 게 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. 받을 주소가 클레임하는 지갑과 다르면서 승인 목록에도 없는 경우입니다. (Airdrop Claim)

#### Audience (대상 사용자)

여러 프로젝트의 에어드롭을 챙기며 클레임을 자주 하는 사용자

#### Used Data (판정에 사용될 데이터)

토큰을 받을 주소(`context.recipient`), 클레임하는 본인 지갑 주소(`principal.address`), 본인이 미리 등록해 둔 수령 주소 목록(`recipientAllowlist`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-not-allowlisted-warn")
@severity("warn")
@reason("이 클레임이 토큰을 본인 지갑도, 승인한 수령 주소 목록에도 없는 주소로 보냅니다 — 받는 주소를 확인하세요")
forbid (
    principal,
    action == Airdrop::Action::"Claim",
    resource
)
when
{
    context.recipient != principal.address &&
    !["0x1111111111111111111111111111111111111111"].contains
        (
            context.recipient
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "airdrop" },
      "action.tag": { "eq": "claim" }
    }
  },
  "_template": {
    "recipientAllowlist": {
      "cedar_set": "[\"0x1111111111111111111111111111111111111111\"]",
      "default": "0x1111111111111111111111111111111111111111",
      "meaning": "Claim recipients you approve in ADDITION to the signing wallet itself (your cold/secondary wallets). MUST be lowercase 0x-hex — the engine lowercases decoded addresses, so a checksummed entry never matches (silent dead no-op). The default is a placeholder example — replace it with your own addresses."
    }
  }
}
```
{% endcode %}

***

**AIRDROP-003: 클레임 수령 주소가 본인 지갑도 승인 목록에도 없을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
