# Lido에서 permit 요청이 무제한 출금 요청일 때 경고

### Policy Definition (정책 정의)

> Lido 출금 요청에 **무제한(최대치) 한도의 permit**이 함께 서명되는 경우 경고합니다.

Lido에서 stETH를 ETH로 출금할 때 토큰 승인과 출금 요청을 permit 서명 한 번으로 끝내는 방식이 있습니다. 그런데 이 permit의 한도가 최대치(무제한)로 잡히면, 이번 출금에 필요한 양뿐 아니라 내 stETH/wstETH 보유량 전체에 대한 사용권이 넘어갑니다.

출금하려는 금액만큼만 한도를 정해도 충분하므로, permit 한도가 무제한으로 잡힌 출금 요청에는 경고를 표시합니다.

#### Scope (적용 범위)

Lido에서 stETH/wstETH 출금을 요청하는 서명에 적용됩니다.

#### Audience (대상 사용자)

Lido에서 stETH/wstETH를 ETH로 출금하는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금 요청 대상이 Lido인지(`context.protocol.name`)
* 요청에 permit이 함께 담겼는지(`context.embedded_permit`)
* 그 permit의 한도(`context.embedded_permit.value`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdrawal-permit-unlimited-warn")
@severity("warn")
@reason("이 출금 요청이 무제한 permit을 서명합니다. 한도를 정한 permit이 더 안전합니다")
forbid (
    principal,
    action == LiquidStaking::Action::"RequestWithdrawal",
    resource
)
when
{
    context.protocol.name == "lido" &&
    context has embedded_permit &&
    context.embedded_permit
        .value == "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdrawal-permit-unlimited-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "request_withdrawal" }
    }
  },
  "_template": {
    "unlimitedAmount": { "cedar_literal": "\"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff\"", "default": "uint256 MAX", "meaning": "the raw-hex permit value treated as unlimited" }
  }
}
```
{% endcode %}

***

**LIDO-009: 출금 요청에 담긴 무제한 한도의 permit 서명 요청 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
