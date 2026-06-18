# RESTAKE-002: 허용 목록에 없는 스트래티지로 리스테이크를 예치할 시 차단

### Policy Definition (정책 정의)

> EigenLayer에 리스테이크를 예치할 때, 예치하려는 **스트래티지(strategy)가 미리 정한 허용 목록에 없으면** 대상을 다시 확인하도록 경고합니다.

EigenLayer에 리스테이크 예치를 한다는 것은 스트래티지(strategy) 컨트랙트에 자산을 넣는 것입니다. 어떤 스트래티지에 예치하느냐에 따라 지분이 묶이는 곳과 슬래싱 위험이 달라지므로, 평소 쓰던 스트래티지가 아니라면 주소가 바뀌었거나 엉뚱한 컨트랙트로 유도됐을 수 있습니다.

이 정책은 예치 대상 스트래티지가 허용 목록에 없으면 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

EigenLayer에 리스테이크를 예치하는 경우에 적용됩니다. 예치 대상 스트래티지가 허용 목록에 들어 있지 않을 때만 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에 직접 리스테이크를 예치하는 사용자

#### Used Data (판정에 사용될 데이터)

* 예치 대상 스트래티지 주소(`context.strategy`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-strategy-not-allowlisted-warn")
@severity("deny")
@reason("허용 목록에 없는 스트래티지로 예치합니다")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when
{
    !(["0x0000000000000000000000000000000000000000"].contains(context.strategy))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-strategy-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } }
}
```
{% endcode %}

***

RESTAKE-002: 허용 목록에 없는 스트래티지로 리스테이크를 예치할 시 차단\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
