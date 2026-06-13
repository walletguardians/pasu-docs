---
description: Transfer of Over Half Your Token Holdings (WARN)
---

# TOKEN-010: 보유량의 절반을 초과하는 토큰을 한 번에 전송할 시 경고

### Policy Definition (정책 정의)

> 한 번의 전송으로 그 토큰 보유량의 절반을 초과해 옮기는 경우 경고합니다.

가진 토큰의 절반이 넘는 양을 한 번에 옮기는 건 의도한 거래일 수도 있지만, 금액을 잘못 입력했거나 지갑이 털리는 중일 때 흔히 보이는 신호이기도 합니다. 전송량이 보유량의 절반을 넘으면, 정말 이만큼 보낼 것인지 서명 전에 확인하도록 경고합니다.

#### Scope (적용 범위)

ERC-20 전송. 전송량이 해당 토큰 보유량의 50%(5,000bp)를 초과하는 경우에 적용됩니다.

#### Audience (대상 사용자)

모든 Wallet 사용자

#### Used Data (판정에 사용될 데이터)

전송량이 보유량에서 차지하는 비중 (`holdingsBp` — 5,000bp = 50%)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-fraction-of-holdings-warn")
@severity("warn")
@reason("이 전송은 해당 토큰 보유량의 절반이 넘는 양을 옮깁니다. 보내기 전에 확인하세요.")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  context has custom
  && context.custom has holdingsBp
  && context.custom.holdingsBp > 5000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-fraction-of-holdings-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_transfer" } } },
  "policy_rpc": [{
    "id": "holdings-fraction",
    "method": "portfolio.input_fraction_bps",
    "params": {
      "chain_id": "$.root.chain_id",
      "owner": "$.root.from",
      "asset": "$.action.token",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "holdingsBp", "type": "Long", "from": "$.result.bps", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "holdingsBp": "Long" } }
}
```
{% endcode %}

***

**TOKEN-010: 보유량의 절반을 초과하는 토큰을 한 번에 전송할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
