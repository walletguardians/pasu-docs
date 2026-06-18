---
description: Changing E-Mode While Debt Is Still Open (WARN)
---

# LEND-013: e-mode를 대출이 남은 상태에서 변경할 시 경고

### Policy Definition (정책 정의)

> 아직 갚지 않은 대출이 남아 있는데 **e-mode**를 바꾸는 경우, 청산 기준선이 달라지니 확인하도록 경고합니다.

Aave의 e-mode(Efficiency Mode)는 가격이 비슷하게 움직이는 자산을 빌릴 때 LTV와 청산 기준을 더 후하게 잡아주는 설정입니다. 이 값을 바꾸는 순간 내 포지션의 청산 가격이 같이 움직이기 때문에, 빚이 남은 상태에서 e-mode를 변경하면 멀쩡해 보이던 포지션이 한순간 청산 위험권에 들어갈 수 있습니다. 그래서 갚지 않은 대출이 있는 채로 e-mode를 변경하면 서명 전에 경고합니다.

#### Scope (적용 범위)

Aave에서 e-mode를 변경하는 트랜잭션. 변경 시점에 갚지 않은 대출 잔액이 남아 있는 경우에 적용됩니다.

#### Audience (대상 사용자)

Aave에서 자산을 빌린 채로 e-mode를 켜고 끄며 자본 효율을 조정하는 사용자

#### Used Data (판정에 사용될 데이터)

* 현재 대출 잔액

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("set-emode-with-open-debt-warn")
@severity("warn")
@reason("대출이 남은 상태에서 e-mode를 바꿉니다 — 청산 기준선이 달라지니 확인하세요")
forbid(principal, action == Lending::Action::"SetEMode", resource)
when {
  context.userStateBefore.totalDebtUsd != "0x0"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "set-emode-with-open-debt-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "set_e_mode" } } }
}
```
{% endcode %}

***

**LEND-013: e-mode를 대출이 남은 상태에서 변경할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
