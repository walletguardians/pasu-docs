---
description: Transfer to a Brand-New Recipient (WARN)
---

# TOKEN-011: 거래 이력이 없는 새 주소로 토큰을 전송할 시 경고

### Policy Definition (정책 정의)

> 거래한 적이 한 번도 없고, 최근에야 처음 나타난 주소로 전송하는 경우 경고합니다.

한 번도 거래한 적 없고 바로 얼마 전에야 체인에 처음 등장한 주소는 오타로 잘못 친 주소이거나, 주소 오염에 쓰인 가짜 주소, 또는 새로 만든 탈취용 주소일 수 있습니다. 받는 곳이 이렇게 '처음 보는' 주소라면, 잠깐의 유예를 두고 주소를 한 번 더 대조하도록 경고합니다.

#### Scope (적용 범위)

ERC-20 전송. 받는 주소의 거래 이력이 0이고 최초로 관측된 시점이 최근 7일(604,800초) 이내인 경우에 적용됩니다.

#### Audience (대상 사용자)

모든 Wallet 사용자

#### Used Data (판정에 사용될 데이터)

받는 주소의 거래 횟수(`recipientTxCount`)와 최초 관측 시각(`recipientFirstSeenTs`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-new-recipient-cooldown-warn")
@severity("warn")
@reason("받는 주소가 거래 이력이 없는 새 주소입니다. 잠시 멈추고 주소를 다시 확인하세요.")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  context has custom
  && context.custom has recipientTxCount
  && context.custom.recipientTxCount == 0
  && context.custom has recipientFirstSeenTs
  && context.custom.recipientFirstSeenTs > (context.meta.submittedAt - 604800)
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-new-recipient-cooldown-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_transfer" } } },
  "policy_rpc": [{
    "id": "recipient-activity",
    "method": "address.activity",
    "params": { "chain_id": "$.root.chain_id", "address": "$.action.recipient" },
    "outputs": [
      { "kind": "context", "field": "recipientTxCount", "type": "Long", "from": "$.result.txCount", "required": false },
      { "kind": "context", "field": "recipientFirstSeenTs", "type": "Long", "from": "$.result.firstSeenTs", "required": false }
    ],
    "optional": true
  }],
  "custom_context": { "fields": { "recipientTxCount": "Long", "recipientFirstSeenTs": "Long" } }
}
```
{% endcode %}

***

**TOKEN-011: 거래 이력이 없는 새 주소로 토큰을 전송할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
