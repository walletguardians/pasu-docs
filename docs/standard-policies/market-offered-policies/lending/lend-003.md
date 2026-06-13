---
description: Supply Crediting a Different aToken Recipient (WARN)
---

# LEND-003: 예치 담보 청구권(aToken)을 본인이 아닌 다른 주소로 받을 시 경고

### Policy Definition (정책 정의)

> 예치로 발행되는 담보 청구권(**aToken**)이 서명하는 본인이 아니라 다른 주소로 들어가는 경우 경고합니다.

Aave에 토큰을 예치하면 그만큼의 담보 청구권을 aToken으로 돌려받습니다. 이 aToken을 쥔 주소가 곧 담보의 주인이라, 나중에 빌리거나 빼는 권한도 그 주소가 갖습니다. 그런데 Aave의 예치에는 청구권을 누구 앞으로 발행할지 지정하는 `onBehalfOf` 칸이 있어서, 여기에 다른 주소가 들어가면 내 돈을 넣고 담보는 남의 것이 됩니다. dApp이 이 칸을 슬쩍 바꿔치기하면 자기 자금을 그대로 잃을 수 있습니다. 그래서 청구권을 받는 주소가 서명자의 주소와 다르면, 정말 의도한 신용 위임인지 서명 전에 확인하도록 경고합니다.

#### Scope (적용 범위)

Aave 같은 대출 프로토콜에 토큰을 예치할 때, 담보 청구권을 받을 주소가 서명하는 본인과 다른 경우에 적용됩니다.

#### Audience (대상 사용자)

대출 프로토콜에 자산을 예치해 담보로 쓰는 사용자

#### Used Data (판정에 사용될 데이터)

청구권을 받을 주소와 이 트랜잭션을 보내는 본인 주소 비교

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("supply-on-behalf-mismatch-warn")
@severity("warn")
@reason("이 예치는 담보 청구권(aToken)을 본인이 아닌 다른 주소로 발행합니다 — 수령 대상을 확인하세요")
forbid(principal, action == Lending::Action::"Supply", resource)
when {
  context has onBehalfOf
  && context.onBehalfOf != context.meta.submitter
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "supply-on-behalf-mismatch-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "supply" } } }
}
```
{% endcode %}

***

**LEND-003: 예치 담보 청구권(aToken)을 본인이 아닌 다른 주소로 받을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
