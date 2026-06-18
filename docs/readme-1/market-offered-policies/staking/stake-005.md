# STAKE-005: Aave 스테이킹 자산을 전액 인출할 시 경고

### Policy Definition (정책 정의)

> Aave Safety Module에 스테이킹한 **잔액 전부**를 한 번에 인출(redeem)하는 경우, 확인 후 서명하도록 경고합니다.

Aave Safety Module에서 인출(redeem)할 때, 일부만 빼려던 게 전량 인출로 나갈 수도 있습니다.

이 정책은 스테이킹된 자산을 전부 인출하려는 경우 경고창을 띄워 한번 더 확인하도록 합니다.

#### Scope (적용 범위)

Aave Safety Module의 스테이킹 인출(redeem)에 적용됩니다.

#### Audience (대상 사용자)

Aave Safety Module에 AAVE를 스테이킹하고(stkAAVE) 직접 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

* 인출을 요청한 대상(`context.venue.name`)
* 인출하려는 양(`context.amount`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-redeem-full-balance-warn")
@severity("warn")
@reason("스테이킹 잔액 전부를 인출하는 것이 맞는지 한번 더 확인해주세요")
forbid(principal, action == Staking::Action::"Redeem", resource)
when {
  context.venue.name == "aave_safety_module"
  && context.amount == "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "aave-redeem-full-balance-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "redeem"
      }
    }
  }
}
```
{% endcode %}

***

STAKE-005: Aave 스테이킹 자산을 전액 인출할 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
