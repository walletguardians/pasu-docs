---
description: Transfer to a Lookalike (Address-Poisoning) Recipient (WARN)
---

# TOKEN-009: 거래 이력이 있는 주소와 닮은 위장 주소로 전송할 시 경고

### Policy Definition (정책 정의)

> 받는 주소가 과거 거래했던 상대와 비슷하게 생겼지만 실제로는 다른 주소인 경우 경고합니다.

주소 오염(address poisoning)은 공격자가 사용자의 거래 상대와 앞뒤 글자가 똑같아 보이는 가짜 주소를 만들어 0원짜리 더미 전송을 미리 보내두는 수법입니다. 사용자가 거래 내역에서 주소를 복사할 때 진짜 대신 이 가짜를 집어 들기를 노린 것이라, 한 번 보내면 자금을 되찾을 수 없습니다. 받는 주소가 과거 상대와 닮았지만 동일하지는 않으면, 한 글자씩 다시 대조하도록 경고합니다.

#### Scope (적용 범위)

ERC-20 전송. 받는 주소가 과거 거래 상대와 앞뒤 글자가 비슷하지만 동일하지 않은 경우에 적용됩니다.

#### Audience (대상 사용자)

모든 Wallet 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소가 과거 거래 상대와 닮았는지 여부 (`poisonCollision` — 주소 유사도 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-address-poisoning-warn")
@severity("warn")
@reason("받는 주소가 예전에 쓰던 주소와 닮았지만 다른 주소입니다. 주소 오염일 수 있으니 꼼꼼히 확인하세요.")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  context has custom
  && context.custom has poisonCollision
  && context.custom.poisonCollision == true
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-address-poisoning-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_transfer" } } },
  "policy_rpc": [{
    "id": "recipient-similarity",
    "method": "address.similarity",
    "params": { "chain_id": "$.root.chain_id", "candidate": "$.action.recipient" },
    "outputs": [{ "kind": "context", "field": "poisonCollision", "type": "Bool", "from": "$.result.poisonCollision", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "poisonCollision": "Bool" } }
}
```
{% endcode %}

***

**TOKEN-009: 거래 이력이 있는 주소와 닮은 위장 주소로 전송할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
