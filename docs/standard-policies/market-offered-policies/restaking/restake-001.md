---
description: Restake Deposit Above a Set Token Cap (WARN)
---

# RESTAKE-001: 리스테이크 예치 수량이 설정한 한도를 넘을 시 경고

### Policy Definition (정책 정의)

> EigenLayer에 한 번에 예치하는 리스테이크 수량이 미리 정해둔 한도를 넘으면, 규모를 다시 확인하도록 경고합니다.

EigenLayer 리스테이크 예치는 들고 있던 토큰을 strategy에 맡겨 리스테이크 지분으로 묶는 첫 단계입니다. 이렇게 맡긴 지분은 곧바로 operator에게 위임돼 슬래싱 대상이 되고, 빼낼 때도 출금 큐를 거쳐야 해서 한 번 들어가면 즉시 되돌리기 어렵습니다. 그래서 한 번에 넣는 수량이 평소보다 크면, 정말 이만한 양을 한꺼번에 맡길 생각이었는지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

EigenLayer에 토큰을 예치해 리스테이크 지분으로 묶는 경우에 적용됩니다. 이번에 예치하는 토큰 수량이 설정한 한도를 넘을 때만 발동합니다. (Deposit)

#### Audience (대상 사용자)

리스테이크 물량을 나눠 넣기보다 한 번에 크게 예치하곤 하는 사용자

#### Used Data (판정에 사용될 데이터)

이번에 예치하는 토큰 수량 (`amountNano` — 정해둔 한도와 비교)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-amount-cap-warn")
@severity("warn")
@reason("설정한 토큰 한도를 넘는 고액 리스테이크 예치입니다 — 수량을 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when { context has amountNano && context.amountNano >= 100000000000 };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-amount-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } }
}
```
{% endcode %}

***

**RESTAKE-001: 리스테이크 예치 수량이 설정한 한도를 넘을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
