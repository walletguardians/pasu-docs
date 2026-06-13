---
description: Transfer to a Sanctioned Address (DENY)
---

# TOKEN-008: 제재 목록에 오른 주소로 토큰을 전송할 시 차단

### Policy Definition (정책 정의)

> 받는 주소가 제재 목록(OFAC·EU·UN)에 오른 경우, 전송이 나가지 않도록 **차단**합니다.

각국 규제기관은 자금세탁, 테러자금 등에 연루된 주소를 제재 목록으로 관리합니다. 이런 주소로 자산을 보내면 법적 책임이 따를 수 있고, 서명이 되면 되돌릴 수 없습니다. 받는 주소가 제재 목록에 올라 있으면 전송 자체를 막습니다.

#### Scope (적용 범위)

받는 주소가 제재 목록에 등재된 경우에 적용됩니다.

#### Audience (대상 사용자)

ERC-20 토큰을 전송 기능을 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소의 제재 목록 등재 여부 (`sanctioned` )를 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-recipient-sanctioned-deny")
@severity("deny")
@reason("받는 주소가 제재 목록(OFAC·EU·UN)에 올라 있어 전송을 차단했습니다 - ")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  context has custom
  && context.custom has sanctioned
  && context.custom.sanctioned == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-recipient-sanctioned-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_transfer" } } },
  "policy_rpc": [{
    "id": "recipient-sanctions",
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

**TOKEN-008: 제재 목록에 오른 주소로 토큰을 전송할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
