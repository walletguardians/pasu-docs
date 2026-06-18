---
description: Airdrop Claim to a Non-Allowlisted Recipient (WARN)
---

# AIRDROP-001: 클레임 시 수령 주소가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고

### Policy Definition (정책 정의)

> 에어드롭 보상 받을 주소가 **미리 승인해 둔 수령 주소 목록에 포함되지 않**는 경우 경고합니다.

에어드롭이나 리워드 클레임은 받을 주소를 따로 지정할 수 있는 경우가 있습니다. 보상을 받을 주소가 내가 서명한 지갑 주소와 다르면서 미리 등록한 허용 목록에도 없으면, 의도한 곳으로 가는 게 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

클레임으로 받을 주소가  승인된 주소 목록과 다른 경우에 적용됩니다.

#### Audience (대상 사용자)

여러 프로젝트의 에어드롭과 리워드를 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

보상을 받을 주소(`context.recipient`)가 서명하는 지갑 주소(`principal.address`)와 같거나 미리 등록해 둔 수령 주소 목록(`recipientAllowlist`)에 포함되는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-not-allowlisted-warn")
@severity("warn")
@reason("클레임 보상을 등록되지 않은 주소로 보냅니다 — 수령 주소를 확인하세요")
forbid (
    principal,
    action == Airdrop::Action::"Claim",
    resource
)
when
{
    context.recipient != principal.address &&
      !([
          "0x1111111111111111111111111111111111111111"
      ].contains(context.recipient))
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

**AIRDROP-001:** 클레임 시 수령 주소가 서명한 주소와 다르거나 승인 목록에 없을 경우 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
