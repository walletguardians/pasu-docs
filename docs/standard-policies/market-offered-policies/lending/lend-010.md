---
description: Borrowing from a Frozen or Paused Reserve (WARN)
---

# LEND-010: 동결·일시정지된 자산 풀에서 대출할 시 경고

### Policy Definition (정책 정의)

> 빌리려는 **자산 풀이 동결(frozen)되거나 일시정지(paused)된 상태**이면, 대출 전에 한 번 더 확인하도록 경고합니다.

Aave 같은 대출 프로토콜은 자산별로 예치·대출을 받는 풀(reserve)을 운영합니다. 어떤 자산에 문제가 생기거나 거버넌스가 위험을 감지하면, 그 풀을 동결하거나 일시정지해 신규 입출금을 막습니다.  이런 풀에서 빚을 내면, 이후 시세가 흔들려도 담보를 더 넣거나 빚을 갚아 청산을 피하지 못할 수 있습니다. 동결되거나 일시정지된 풀에서 대출을 시도하면, 서명 전에 경고합니다.

#### Scope (적용 범위)

대출 프로토콜의 대출(Borrow) 동작. 빌리려는 자산 풀이 동결 또는 일시정지된 상태일 때 적용됩니다.

#### Audience (대상 사용자)

Aave·Morpho 등에서 담보를 잡고 빚을 내 운용하는 사용자

#### Used Data (판정에 사용될 데이터)

* 빌리려는 자산 풀의 동결 여부
* 빌리려는 자산 풀의 일시정지 여부

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
\&#xNAN;_Supported Chain: Ethereum_
