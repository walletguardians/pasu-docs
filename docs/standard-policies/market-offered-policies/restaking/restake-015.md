---
description: Registering the Wallet as an EigenLayer Operator (DENY)
---

# RESTAKE-015: EigenLayer 오퍼레이터로 등록할 시 차단

### Policy Definition (정책 정의)

> 이 지갑을 EigenLayer **오퍼레이터로 등록**하려는 트랜잭션을 차단합니다.

EigenLayer에서 오퍼레이터는 다른 리스테이커들이 지분을 위임하는 대상입니다. 위임을 받은 오퍼레이터는 AVS 운영 책임을 지고, 잘못 굴리면 위임받은 지분까지 함께 슬래싱당합니다. 그냥 토큰을 맡겨 수익만 받으려던 리스테이커가 오퍼레이터 등록까지 하는 경우는 거의 없고, 보통은 본인도 모르게 권한이 딸려 들어간 트랜잭션이거나 등록을 유도하는 피싱입니다. 오퍼레이터로 등록되면 위임·슬래싱 구도의 책임 주체로 바뀌므로, 이 등록 트랜잭션은 경고가 아니라 서명 전에 막습니다.

#### Scope (적용 범위)

EigenLayer DelegationManager에 이 지갑을 오퍼레이터로 등록하는 트랜잭션에 적용됩니다. 등록 조건이나 금액과 무관하게, 오퍼레이터 등록 동작이면 항상 발동합니다. (Register Operator)

#### Audience (대상 사용자)

직접 운영에 나서지 않고 지분만 맡겨두려는 패시브 리스테이커

#### Used Data (판정에 사용될 데이터)

트랜잭션의 동작 종류 하나만 봅니다. 디코딩한 동작이 오퍼레이터 등록(`action.tag == register_operator`)이면 그대로 차단하며, 외부 조회나 금액 기준은 쓰지 않습니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("register-as-operator-deny")
@severity("deny")
@reason("EigenLayer 오퍼레이터로 등록하려 합니다 — 패시브 리스테이커는 오퍼레이터가 되어선 안 되므로 차단했습니다")
forbid (
    principal,
    action == Restaking::Action::"RegisterOperator",
    resource
);
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "register-as-operator-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "register_operator" } } }
}
```
{% endcode %}

***

**RESTAKE-015: EigenLayer 오퍼레이터로 등록할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
