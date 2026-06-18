---
description: Swap Output Destined for a Third-Party Address (WARN)
---

# AMM-001: Swap으로 받을 자산이 제3자에게 갈 시 경고

### Policy Definition (정책 정의)

> Swap할 때 토큰을 수령할 주소가 **제3자의 것**으로 되어 있는 경우, 사용자가 수령할 주소를 검토할 수 있도록 **경고**합니다.

Swap은 내가 보유한 토큰을 다른 토큰으로 교환하는 것을 말합니다. 이때 토큰을 받는 주소가 다른 주소로 지정되어 있을 수도 있기 때문에, 토큰을 받을 주소가 내 주소가 아닌 다른 것으로 되어 있으면 경고를 표시합니다.

#### Scope (적용 범위)

AMM 기반의 DEX에서 Swap할 때 적용됩니다. _(Beta: Uniswap Only)_

#### Audience (대상 사용자)

DEX를 사용하는 모든 사용자

#### Used Data (판정에 사용될 데이터)

서명에 사용할 지갑의 주소(`principal.address`)와 Swap을 통해 토큰을 받을 주소

#### Disclaimer (주의 사항)

Uniswap Universal Router를 사용하는 것과 같은 상황에선, Swap의 recipient가 `0x0000...0001` ('서명자에게')과 같이 예약어)로 되어 있습니다. 이 경우 `context.recipient ≠ principal.address` 가 되어 경고가 표시될 수 있으므로, 이를 방지하기 위한 예외 처리가 되어 있습니다. 이상을 식별하는 데에 있어 문제가 있는 것은 아닙니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("swap-recipient-not-self-warn")
@severity("warn")
@reason("Swap 후 토큰이 제3자에게 전송될 수 있습니다. 거래 설정이 맞는지 확인하세요.")
forbid(principal, action == Amm::Action::"Swap", resource)
when {
  context.recipient != principal.address
  && !([
    "0x0000000000000000000000000000000000000001", // UR MSG_SENDER (= signer)
    "0x0000000000000000000000000000000000000002"  // UR ADDRESS_THIS (= router, normal routing)
  ].contains(context.recipient))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "swap-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "swap"
      }
    }
  }
}
```
{% endcode %}

***

AMM-001: Swap으로 받을 자산이 제3자에게 갈 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/09\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
