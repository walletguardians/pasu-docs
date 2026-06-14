# 허용 목록에 없는 오퍼레이터에게 위임할 시 차단

### Policy Definition (정책 정의)

> 화이트리스팅 되지 않은 오퍼레이터에게 리스테이크 지분을 위임하려 하면, 위임을 **차단**합니다.

EigenLayer에서 위임이란 내가 예치해 둔 리스테이크 지분의 운용 권한을 특정 오퍼레이터에게 넘기는 일입니다. 한 번 위임하면 그 오퍼레이터가 내 지분으로 AVS 검증에 참여하고, 잘못하면 내 지분이 슬래싱될 수도 있습니다. 그래서 누구에게 맡기느냐가 곧 내 자산의 안전과 직결됩니다.

이 정책은 내가 직접 신뢰 목록에 올려둔 오퍼레이터로만 위임을 허용하고, 목록에 없는 주소로 위임하려 하면 차단합니다. 처음 보는 오퍼레이터에게 위임하려면 그 주소를 허용 목록에 추가해야 합니다.

**! 이 정책을 사용하려면 오퍼레이터 허용 목록을 설정해야 합니다.**

#### Scope (적용 범위)

EigenLayer에서 오퍼레이터에게 리스테이크 지분을 위임하는 경우에 적용됩니다.

#### Audience (대상 사용자)

EigenLayer에서 직접 고른 오퍼레이터에게만 위임을 묶어두고 싶은 리스테이커

#### Used Data (판정에 사용될 데이터)

* 위임을 받는 오퍼레이터 주소(`context.operator`)
* 사용자가 미리 정해 둔 허용 오퍼레이터 주소 목록

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("delegate-operator-not-allowlisted-deny")
@severity("deny")
@reason("허용 목록에 없는 오퍼레이터 주소입니다. 허용 목록을 확인해주세요")
forbid (
    principal,
    action == Restaking::Action::"DelegateTo",
    resource
)
when
{
    !(["0x0000000000000000000000000000000000000000"].contains(context.operator))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "delegate-operator-not-allowlisted-deny",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "delegate_to" } } }
}
```
{% endcode %}

***

**RESTAKE-007: 허용 목록에 없는 오퍼레이터에게 위임할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
