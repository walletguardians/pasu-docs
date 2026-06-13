# EigenLayer 오퍼레이터로 등록할 시 경고

### Policy Definition (정책 정의)

> 이 지갑을 EigenLayer **오퍼레이터로 등록**하려는 트랜잭션을 경고합니다.

EigenLayer에서 오퍼레이터는 다른 리스테이커들이 지분을 위임하는 대상입니다. 위임을 받은 오퍼레이터는 AVS 운영 책임을 지고, 잘못 굴리면 위임받은 지분까지 함께 슬래싱당합니다. 그냥 토큰을 맡겨 수익만 받으려던 리스테이커가 오퍼레이터 등록까지 하는 경우는 거의 없고, 보통은 본인도 모르게 등록을 유도하는 피싱일 확률이 높습니다.

#### Scope (적용 범위)

EigenLayer DelegationManager에 이 지갑을 오퍼레이터로 등록하는 트랜잭션에 적용됩니다. 등록 조건이나 금액과 무관하게, 오퍼레이터 등록 동작이면 항상 발동합니다. (Register Operator)

#### Audience (대상 사용자)

리스테이킹 수익만 받고 싶은 사람

#### Used Data (판정에 사용될 데이터)

트랜잭션의 동작 종류 하나만 봅니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("register-as-operator-deny")
@severity("warn")
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

**RESTAKE-015: EigenLayer 오퍼레이터로 등록할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
