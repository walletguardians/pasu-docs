---
description: Third-Party Transfer of Withdrawn Tokens (WARN)
---

# AMM-003: 유동성 풀(LP)에서 유동성을 회수할 때, 회수한 토큰이 제3자에게 갈 시 경고

### Policy Definition (정책 정의)

> 사용자가 유동성 풀(Liquid Pool)에 유동성을 공급할 때, LP Token 혹은 포지션 NFT가 제3자에게 전달이 될 경우, 상황을 검토할 수 있도록 경고를 표시합니다.

유동성 풀의 일정 지분을 소유한다는 증표인 LP Token 또는 포지션 NFT를 반납하고 토큰을 회수할 때, 회수할 토큰을 받을 주소가 제3자 소유의 지갑으로 설정되어 있을 경우 사용자는 토큰을 돌려받지 못하게 됩니다. 따라서 유동성이 사용자 주소가 아닌 제3자에게 지급되는 경우 경고를 표시합니다.

#### Scope (적용 범위)

유동성 풀에 토큰을 회수하는 경우에 적용됩니다.

#### Audience (대상 사용자)

DEX를 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터)

* 서명에 사용할 지갑의 주소
* 토큰을 받을 주소

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ammlp-remove-recipient-not-self-warn")
@severity("warn")
@reason("회수한 유동성이 다른 지갑으로 전송됩니다. 수취인이 바르게 설정되었는지 거래를 검토하세요.")
forbid(principal, action == Amm::Action::"RemoveLiquidity", resource)
when {
  context.params has recipient
  && context.params.recipient != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "ammlp-remove-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "remove_liquidity"
      }
    }
  }
}
```
{% endcode %}

***

**AMM-003: 유동성 풀(LP)에서 유동성을 회수할 때, 회수한 토큰이 제3자에게 갈 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/10\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
