---
description: Token Transfer to Burn Address (DENY)
---

# TOKEN-002: 토큰이 소각 주소로 전송되는 경우 차단

### Policy Definition (정책 정의)

> 토큰이 소각될 수 있는 주소로 전송되는 경우 차단합니다.

ERC-20 토큰 소각(Burning)은 토큰을 영구히 파괴하여 유통된 전체 토큰에서 특정 토큰의 수량 일부를 제거하는 행위입니다. 사용자가 소각 주소로 토큰을 전송하려고 하면 서명 자체를 차단합니다.&#x20;

#### Scope (적용 범위)

모든 ERC-20 transfer에 적용됩니다.

#### Audience (대상 사용자)

ERC-20 토큰을 보유중인 모든 사용자

#### Used Data (판정에 사용될 데이터)

토큰을 받을 주소(`context.recipient`)가 토큰을 소각 시키는 지정된 주소인지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("send-burn-recipient-deny")
@severity("deny")
@reason("소각 주소로 자산이 전송됩니다 - 수신자가 정확한지 확인하세요")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  ["0x0000000000000000000000000000000000000000",
   "0x000000000000000000000000000000000000dead"].contains(context.recipient)
};

```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "send-burn-recipient-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "erc20_transfer"
      }
    }
  }
}
```
{% endcode %}

***

TOKEN-002: 토큰이 소각 주소로 전송되는 경우 차단\
Wallet Guardians | v.1.0.0 | 26/06/09\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
