# 거액을 리스테이킹하는 경우 경고

### Policy Definition (정책 정의)

> EigenLayer에 한 번에 예치하는 리스테이크 수량이 미리 정해둔 한도를 넘으면, 규모를 다시 확인하도록 경고합니다.

EigenLayer에 리스테이킹된 자산은 슬래싱 대상이 될 수도 있고, 빼낼 때도 시간이 걸립니다.

이 정책은 Eigenlayer에 거액을 예치하는 경우, 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

EigenLayer에 토큰을 예치해 리스테이크 지분으로 묶는 경우에 적용됩니다.

#### Audience (대상 사용자)

Eigenlayer 리스테이킹 사용자

#### Used Data (판정에 사용될 데이터)

이번에 예치하는 토큰 수량 (`amountNano` — 정해둔 한도와 비교)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-amount-cap-warn")
@severity("warn")
@reason("리스테이킹을 하는 경우 출금에 오랜 시간이 걸릴 수 있습니다.")
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
\&#xNAN;_Supported Chain: Ethereum_
