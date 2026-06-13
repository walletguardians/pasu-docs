---
description: Airdrop Claim Redirected to a Sanctioned Address (DENY)
---

# AIRDROP-004: 에어드롭 보상을 제재 목록에 오른 주소로 받을 시 차단

### Policy Definition (정책 정의)

> 에어드롭 보상을 받을 주소가 **내 지갑이 아니면서** 제재 목록(OFAC·EU·UN)에 올라 있으면 클레임을 **차단**합니다.

에어드롭 클레임은 받을 주소를 따로 지정할 수 있어서, 보상이 내 지갑이 아닌 다른 주소로 빠져나가도록 만들 수 있습니다. 그 주소가 각국 규제기관이 자금세탁·테러자금 연루로 올려둔 제재 목록에 들어 있으면, 보상을 보내는 순간 법적 책임이 따르고 한 번 나간 토큰은 회수할 수도 없습니다. 따라서 받을 주소가 내 지갑이 아니면서 제재 목록에 올라 있으면 경고에 그치지 않고 클레임 자체를 막습니다.

#### Scope (적용 범위)

에어드롭·리워드 토큰 클레임에 적용됩니다. (Claim) 받을 주소가 서명하는 본인 지갑과 다르고, 그 주소가 제재 목록에 등재된 경우에 한합니다.

#### Audience (대상 사용자)

에어드롭이나 리워드를 클레임하면서 받을 주소를 직접 입력해 본 적 있는 사용자

#### Used Data (판정에 사용될 데이터)

받을 주소(`context.recipient`)가 서명자 본인 지갑(`principal.address`)과 같은지, 그리고 그 주소의 제재 목록 등재 여부(`sanctioned` — 제재 목록 조회 결과)를 봅니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-recipient-sanctioned-deny")
@severity("deny")
@reason("이 에어드롭 클레임의 수령 주소(본인 아님)가 제재 목록(OFAC·EU·UN)에 올라 있어 차단했습니다")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context.recipient != principal.address
  && context has custom
  && context.custom has sanctioned
  && context.custom.sanctioned == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-recipient-sanctioned-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } },
  "policy_rpc": [{
    "id": "claim-recipient-sanctions",
    "method": "address.sanctions",
    "params": { "address": "$.action.recipient" },
    "outputs": [{ "kind": "context", "field": "sanctioned", "type": "Bool", "from": "$.result.sanctioned", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "sanctioned": "Bool" } }
}
```
{% endcode %}

***

**AIRDROP-004: 에어드롭 보상을 제재 목록에 오른 주소로 받을 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
