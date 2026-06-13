# STAKE-014: sUSDe 인출 자금 수령자가 본인이 아닌 경우 차단

### Policy Definition (정책 정의)

> Ethena의 sUSDe를 인출하면서 받은 USDe를 **본인 지갑이 아닌** 주소로 보내는 경우, 인출이 나가지 않도록 **차단**합니다.

Ethena에서 sUSDe 인출(Redeem)은 그동안 맡겨둔 sUSDe를 다시 USDe로 바꿔 빼오는 동작입니다.

그런데 인출 함수는 맡겨두었던 USDe 수령 주소를 따로 지정할 수 있습니다. 수령 주소가 본인 지갑이 아닌 경우, USDe가 엉뚱한 주소로 전송됩니다. 수령 주소가 인출을 요청한 본인 지갑과 다르면 차단합니다.

#### Scope (적용 범위)

Ethena sUSDe의 인출에 적용됩니다. USDe를 받을 주소가 인출을 요청한 본인 지갑 주소와 다른 경우 발동합니다.

#### Audience (대상 사용자)

Ethena에 USDe를 맡겨 sUSDe를 보유하고, 직접 다시 USDe로 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

* 인출 프로토콜(`context.protocol.name`)
* USDe를 받을 주소(`context.recipient`)
* 인출을 요청한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ethena-redeem-recipient-self")
@severity("deny")
@reason("이 sUSDe redeem 이 인출한 USDe를 본인 지갑이 아닌 주소로 보냅니다 — 차단했습니다")
forbid (
    principal,
    action == Staking::Action::"Redeem",
    resource
)
when
{
    context.protocol.name == "ethena_staked_usde" &&
    context has recipient &&
    context.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "ethena-redeem-recipient-self",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "staking" },
      "action.tag": { "eq": "redeem" }
    }
  }
}
```
{% endcode %}

***

**STAKE-014: sUSDe 인출 자금을 본인이 아닌 주소로 보낼 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
