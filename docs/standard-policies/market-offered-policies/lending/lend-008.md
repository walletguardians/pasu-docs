---
description: Withdrawal Sent to an Address Other Than Yourself (WARN)
---

# LEND-008: 대출 자산을 본인이 아닌 주소로 인출할 시 경고

### Policy Definition (정책 정의)

> 예치한 자산을 인출하면서 받는 주소가 **본인 지갑이 아닌** 경우, 받는 주소를 확인하도록 경고합니다.

대출 프로토콜에서 인출(Withdraw)은 그동안 예치해둔 자산을 다시 내 지갑으로 빼오는 동작입니다. 대부분의 인출 함수는 자금을 받을 주소를 따로 지정할 수 있어, 이 주소가 바뀌면 빼온 자산이 내 지갑이 아닌 다른 곳으로 흘러갑니다. dApp이 미리 채워준 주소를 그대로 서명했다가 자산을 통째로 잃는 등의 피싱에 당하기도 합니다. 받는 주소가 인출을 요청한 본인 지갑과 다르면, 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

대출 프로토콜의 인출에 적용됩니다. 받는 주소가 인출을 요청한 본인 지갑 주소와 다른 경우 발동합니다.

#### Audience (대상 사용자)

Aave, Morpho 등에 자산을 예치하고 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

* 자산을 받을 주소
* 인출을 요청한 본인 지갑 주소

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdraw-recipient-not-self-warn")
@severity("warn")
@reason("이 인출은 본인 지갑이 아닌 주소로 자금을 보냅니다 — 받는 주소를 확인하세요")
forbid(principal, action == Lending::Action::"Withdraw", resource)
when {
  context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdraw-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "withdraw"
      }
    }
  }
}
```
{% endcode %}

***

**LEND-008: 대출 자산을 본인이 아닌 주소로 인출할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
