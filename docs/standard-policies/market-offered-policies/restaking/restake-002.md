# 내 예치분이 내 지갑이 아닌 주소로 잡힐 시 차단

### Policy Definition (정책 정의)

> **본인 지갑이 아닌 주소**로 예치(Deposit) 지분이 잡히는 경우, 서명 전에 내용을 다시 확인하도록 차단합니다.

EigenLayer에서의 예치는 토큰을 스트래티지(strategy)에 넣으면, 그 대가로 리스테이크 지분이 특정 주소(staker)에 적립됩니다. 만약 이 주소가 서명하는 본인이 아닌 다른 주소로 설정되면, 예치 자금은 내 지갑에서 나가지만 그에 대한 지분과 이후 출금·위임 권한은 그 다른 주소가 갖게 됩니다.

지분을 받을 주소가 내 주소가 아니면 차단합니다.

#### Scope (적용 범위)

EigenLayer 리스테이킹 예치에 적용됩니다. 지분을 받을 주소(staker)가 이 트랜잭션을 보내는 본인 지갑과 다른 경우에만 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에 자산을 예치해 리스테이킹하는 사용자

#### Used Data (판정에 사용될 데이터)

* 지분을 받을 주소(`context.staker`)
* 이 트랜잭션을 보내는 본인 지갑(`context.meta.submitter`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-on-behalf-other-staker-warn")
@severity("deny")
@reason("서명하는 예치(Deposit)의 스테이커가 본인 지갑이 아닙니다 — 서명 전에 내용을 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when { context has staker && context.staker != context.meta.submitter };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-on-behalf-other-staker-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } }
}
```
{% endcode %}

***

**RESTAKE-002: 예치 지분이 본인이 아닌 다른 주소로 잡힐 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
