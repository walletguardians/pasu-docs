---
description: Transfer to the Token's Own Contract (WARN)
---

# TOKEN-012: 토큰의 컨트랙트 주소로 그 토큰을 전송할 시 경고

### Policy Definition (정책 정의)

> 토큰을 그 토큰 자신의 컨트랙트 주소로 전송하는 경우 경고합니다.

토큰 주소와 받는 사람 주소를 헷갈려, 보내려는 토큰의 컨트랙트 주소를 수신자 칸에 그대로 붙여넣는 실수가 의외로 잦습니다. 대부분의 ERC-20 컨트랙트는 이렇게 들어온 토큰을 돌려주는 기능이 없어, 한 번 보내면 사실상 영영 묶여버립니다. 받는 주소가 바로 그 토큰의 컨트랙트 주소와 같으면, 보내기 전에 경고합니다.

#### Scope (적용 범위)

ERC-20 전송. 받는 주소가 전송하려는 토큰의 컨트랙트 주소와 같은 경우에 적용됩니다.

#### Audience (대상 사용자)

모든 Wallet 사용자

#### Used Data (판정에 사용될 데이터)

보내려는 토큰의 컨트랙트 주소(`token.key.address`)와 받는 주소(`context.recipient`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-to-token-contract-warn")
@severity("warn")
@reason("받는 주소가 전송하려는 토큰 자체의 컨트랙트 주소입니다. 보낸 토큰이 영영 묶일 수 있으니 확인하세요.")
forbid(principal, action == Token::Action::"Erc20Transfer", resource)
when {
  context.token.key has address
  && context.recipient == context.token.key.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-to-token-contract-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_transfer" } } }
}
```
{% endcode %}

***

**TOKEN-012: 토큰의 컨트랙트 주소로 그 토큰을 전송할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
