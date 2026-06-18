---
description: Third-Party Transfer of LP Fees (WARN)
---

# AMM-004: LP 수수료가 제3자에게 갈 시 경고

### Policy Definition (정책 정의)

> 사용자가 유동성 풀(Liquid Pool)에 유동성을 공급하고 받은 LP 수수료가 제3자에게 전달될 경우, 사용자가 상황을 검토할 수 있도록 경고를 표시합니다.

유동성 풀의 일정 지분을 소유한다는 증표인 LP Token 또는 포지션 NFT를 보유하고 있으면 거래 수수료가 계약에 맞게 쌓이고, 이를 이후 회수할 수 있게 됩니다. 이때 회수할 토큰을 받을 주소가 제3자 소유의 지갑으로 설정될 경우 사용자는 토큰을 돌려받지 못하게 됩니다. 따라서 수수료 보상이 제3자에게 지급되는 경우 경고를 표시합니다.

#### Scope (적용 범위)

유동성 풀에 자산을 예치하고 수수료를 받을 때 적용됩니다.

#### Audience (대상 사용자)

DEX를 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터)

* 서명에 사용할 지갑의 주소
* 토큰을 받을 주소

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("ammlp-collect-recipient-not-self-warn")
@severity("warn")
@reason("유동성 수수료를 받을 주소가 내 지갑이 아닙니다. 수수료가 다른 주소로 빠져나갈 수 있으니 트랜잭션을 검토하세요.")
forbid(principal, action == Amm::Action::"CollectFees", resource)
when { 
    context.recipient != principal.address 
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "ammlp-collect-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "collect_fees"
      }
    }
  }
}
```
{% endcode %}

***

**AMM-004: LP 수수료가 제3자에게 갈 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/10\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
