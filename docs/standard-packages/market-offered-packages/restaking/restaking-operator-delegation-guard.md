# \[Restaking] 스트래티지 예치/오퍼레이터 위임 가드

스트래티지 예치나 오퍼레이터 위임은 리스테이킹 지분 전체에 대한 슬래싱 권한을 넘기는 동작입니다. 즉, 잘못하면 내 자산이 슬래싱될 수 있습니다.&#x20;

이 패키지는 위임/재위임하는 오퍼레이터나 예치하는 스트래티지가 신뢰할 수 있는지 검사하고, 의도치 않게 오퍼레이터로 등록되는 것을 막는 패키지입니다.

**이 패키지를 사용하기 위해서는 허용 목록을 편집해서 사용해야 합니다.**



### 이 패키지로 막을 수 있는 것

리스테이킹 지분을 오퍼레이터에게 위임하는 사용자가, 슬래싱 권한을 엉뚱한 오퍼레이터에게 넘기지 않도록 하고, 의도치 않게 오퍼레이터로 등록되는 것을 막습니다.

* 허용 목록에 없는 스트래티지로 예치할 시 경고합니다.
* 허용 목록에 없는 오퍼레이터에게 위임/재위임하지 않도록 차단합니다.
* 의도치 않게 오퍼레이터로 등록되는 것을 막습니다.

### 정책 목록

{% content-ref url="../../../readme-1/market-offered-policies/restaking/restake-002.md" %}
[restake-002.md](../../../readme-1/market-offered-policies/restaking/restake-002.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/restaking/restake-004.md" %}
[restake-004.md](../../../readme-1/market-offered-policies/restaking/restake-004.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/restaking/restake-005.md" %}
[restake-005.md](../../../readme-1/market-offered-policies/restaking/restake-005.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/restaking/restake-007.md" %}
[restake-007.md](../../../readme-1/market-offered-policies/restaking/restake-007.md)
{% endcontent-ref %}

***

\[Restaking] 스트래티지 예치/오퍼레이터 위임 가드\
Wallet Guardians | v.1.0.0 | 26/06/11\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
