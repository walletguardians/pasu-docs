---
description: Full Collateral Withdrawal While Debt Remains (WARN)
---

# LEND-009: 대출이 남은 상태에서 예치 잔액을 전부 인출할 시 경고

### Policy Definition (정책 정의)

> 갚지 않은 대출이 남아 있는데 예치한 담보를 **전부** 빼가는 인출이면, 청산 위험을 다시 확인하도록 경고합니다.

Aave나 Morpho 같은 대출 프로토콜에서는 예치한 자산이 곧 대출의 담보가 됩니다. 담보를 빼낼수록 health factor가 내려가고, 1 밑으로 떨어지면 포지션이 청산됩니다. 그래서 대출이 남은 상태에서 예치 잔액을 한도 끝까지(uint256 max) 인출하면 담보가 거의 바닥나, 시세가 조금만 움직여도 청산으로 이어질 수 있습니다. 인출하려는 수량이 잔액 전부를 뜻하는 값이고 갚지 않은 대출이 남아 있으면, 정말 이만큼 빼도 되는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜의 담보 인출(Withdraw). 인출 수량이 예치 잔액 전부이고, 인출 시점에 갚지 않은 대출이 남아 있는 경우에 적용됩니다.

#### Audience (대상 사용자)

대출을 끼고 담보를 예치해 둔, 청산 위험에 노출된 사용자

#### Used Data (판정에 사용될 데이터)

* 인출 수량&#x20;
* 미상환 대출 규모

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("withdraw-full-balance-confirm-warn")
@severity("warn")
@reason("대출이 남은 상태에서 예치 잔액 전부를 인출합니다 — 포지션이 청산에 가까워질 수 있으니 확인하세요")
forbid(principal, action == Lending::Action::"Withdraw", resource)
when {
  context.amount == "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
  && context.userStateBefore.totalDebtUsd != "0x0"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "withdraw-full-balance-confirm-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "withdraw" } } }
}
```
{% endcode %}

***

**LEND-009: 대출이 남은 상태에서 예치 잔액을 전부 인출할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
