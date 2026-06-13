---
description: Redeeming the Entire Aave Safety Module Stake (WARN)
---

# STAKE-006: Aave Safety Module 스테이킹 잔액 전부를 인출할 시 경고

### Policy Definition (정책 정의)

> Aave Safety Module에 스테이킹한 **잔액 전부**를 한 번에 인출(redeem)하는 경우, 확인 후 서명하도록 경고합니다.

Aave Safety Module은 AAVE를 맡기면(stkAAVE) 보상을 주되, 프로토콜에 부족 사태가 생기면 맡긴 물량으로 손실을 메우는 구조입니다. 그래서 인출 함수에 잔액 전부를 뜻하는 최대치(`uint256.max`)를 넣으면 맡겨둔 스테이킹 지분을 통째로 빼내게 됩니다. 보상과 슬래싱 보호를 한꺼번에 내려놓는 동작이라 의도와 어긋나면 되돌리기 번거롭고, dApp이 채워준 값을 그대로 서명했다가 일부만 빼려던 게 전량 인출로 나가기도 합니다. 인출하려는 양이 스테이킹 잔액 전부에 해당하면, 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

Aave Safety Module의 스테이킹 인출에 적용됩니다. 인출하려는 양이 잔액 전부를 뜻하는 최대치(`uint256.max`)로 들어온 경우 발동합니다. (Redeem)

#### Audience (대상 사용자)

Aave Safety Module에 AAVE를 스테이킹하고(stkAAVE) 직접 인출하는 사용자

#### Used Data (판정에 사용될 데이터)

인출을 요청한 대상(`context.venue.name`)과 인출하려는 양(`context.amount` — 잔액 전부를 뜻하는 최대치인지)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("aave-redeem-full-balance-warn")
@severity("warn")
@reason("이 Aave redeem 은 스테이킹 잔액 전부를 인출합니다(amount = 최대치) — 확인 후 서명하세요")
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

**STAKE-006: Aave Safety Module 스테이킹 잔액 전부를 인출할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
