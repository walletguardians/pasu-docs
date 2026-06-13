---
description: Third-Party Transfer of LP Tokens and Position NFTs (WARN)
---

# AMM-002: 유동성 풀(LP)에 유동성을 공급할 때, LP Token 또는 포지션 NFT가 제3자에게 갈 시 경고

### Policy Definition (정책 정의)

> 사용자가 유동성 풀(Liquid Pool)에 유동성을 공급할 때, LP Token 혹은 포지션 NFT가 제3자에게 전달이 될 경우, 상황을 검토할 수 있도록 경고를 표시합니다.

유동성을 공급하는 것은 토큰을 풀에 공급한 대가로 그 풀의 일정 지분을 소유한다는 증표를 받는 행위입니다. 그 증표인 LP Token 또는 포지션 NFT가 제3자에게 전달되면, 사용자는 유동성을 공급하더라도 그 유동성을 회수할 권리를 갖지 못하게 됩니다. 유동성을 회수할 권리가 제3자에게 지급되는 경우 경고를 표시합니다.

#### Scope (적용 범위)

유동성 풀에 토큰을 공급하는 경우에 적용됩니다. (Add Liquidity)

#### Audience (대상 사용자)

DEX를 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터)

* 서명에 사용할 지갑의 주소
* 유동성 공급을 통해 토큰을 받을 주소&#x20;

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ammlp-add-recipient-not-self-warn")
@severity("warn")
@reason("이 유동성 공급은 다른 주소로 LP Token을 전송합니다. 수취인이 바르게 설정되었는지 거래를 검토하세요.")
forbid(principal, action == Amm::Action::"AddLiquidity", resource)
when { 
    context.params has recipient 
    && context.params.recipient != principal.address 
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "ammlp-add-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "add_liquidity"
      }
    }
  }
}
```
{% endcode %}

***

**AMM-002: 유동성 풀(LP)에 유동성을 공급할 때, LP Token 또는 포지션 NFT가 제3자에게 갈 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/10\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
