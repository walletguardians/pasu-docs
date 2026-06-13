---
description: Borrowing from a Frozen or Paused Reserve (WARN)
---

# LEND-010: 동결·일시정지된 자산 풀에서 대출할 시 경고

### Policy Definition (정책 정의)

> 빌리려는 **자산 풀이 동결(frozen)되거나 일시정지(paused)된 상태**이면, 대출 전에 한 번 더 확인하도록 경고합니다.

대출 프로토콜은 자산별로 풀을 운영하는데, 가격 이상이나 보안 문제가 생기면 그 풀을 동결하거나 일시정지로 묶어둡니다. 묶인 동안에는 대출은 물론 상환·인출 같은 포지션 관리 동작도 막히거나 정상적으로 처리되지 않습니다. 이런 풀에서 빚을 내면, 이후 시세가 흔들려도 담보를 더 넣거나 빚을 갚아 청산을 피하는 길이 막힐 수 있습니다. 그래서 동결되거나 일시정지된 풀에서 대출을 시도하면, 서명 전에 경고합니다.

#### Scope (적용 범위)

대출 프로토콜의 대출(Borrow) 동작. 빌리려는 자산 풀이 동결 또는 일시정지된 상태일 때 적용됩니다.

#### Audience (대상 사용자)

Aave·Morpho 등에서 담보를 잡고 빚을 내 운용하는 사용자

#### Used Data (판정에 사용될 데이터)

빌리려는 자산 풀의 동결 여부(`context.reserveState.isFrozen`)와 일시정지 여부(`context.reserveState.isPaused`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("borrow-into-frozen-reserve-warn")
@severity("warn")
@reason("이 자산 풀이 동결(frozen)·일시정지(paused) 상태입니다 — 빌리면 포지션을 관리하지 못할 수 있으니 확인하세요")
forbid(principal, action == Lending::Action::"Borrow", resource)
when {
  context.reserveState.isFrozen || context.reserveState.isPaused
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "borrow-into-frozen-reserve-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "borrow" } } }
}
```
{% endcode %}

***

**LEND-010: 동결·일시정지된 자산 풀에서 대출할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
