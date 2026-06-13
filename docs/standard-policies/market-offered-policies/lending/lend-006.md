---
description: Supply into a Frozen or Paused Reserve (WARN)
---

# LEND-006: 동결·일시정지된 자산 풀에 예치할 시 경고

### Policy Definition (정책 정의)

> 예치하려는 자산 풀이 **동결(frozen)** 또는 **일시정지(paused)** 상태인 경우, 자금이 묶일 수 있으니 다시 확인하도록 경고합니다.

Aave 같은 대출 프로토콜은 자산별로 예치·대출을 받는 풀(reserve)을 운영합니다. 어떤 자산에 문제가 생기거나 거버넌스가 위험을 감지하면, 그 풀을 동결하거나 일시정지해 신규 입출금을 막습니다. 이때 들어간 예치금은 풀이 다시 열릴 때까지 인출도, 담보 활용도 못 하고 묶이게 됩니다. 그래서 예치하려는 풀이 동결이나 일시정지 상태로 조회되면, 예치를 넣기 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜에 자산을 예치하는 경우. 그 풀이 동결 또는 일시정지 상태일 때 적용됩니다. (Supply)

#### Audience (대상 사용자)

Aave·Morpho 등 대출 프로토콜에 자산을 예치하는 사용자

#### Used Data (판정에 사용될 데이터)

예치하려는 풀의 동결 여부(`context.reserveState.isFrozen`)와 일시정지 여부(`context.reserveState.isPaused`) — 풀 상태 조회 결과

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("supply-into-frozen-reserve-warn")
@severity("warn")
@reason("이 자산 풀이 동결(frozen)·일시정지(paused) 상태입니다 — 예치하면 자금이 묶일 수 있으니 확인하세요")
forbid(principal, action == Lending::Action::"Supply", resource)
when {
  context.reserveState.isFrozen || context.reserveState.isPaused
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "supply-into-frozen-reserve-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "supply" } } }
}
```
{% endcode %}

***

**LEND-006: 동결·일시정지된 자산 풀에 예치할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
