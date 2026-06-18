---
description: 자산이 의도치 않은 체인으로 가거나 도착 후 위험한 코드가 실행되는 것을 막는 패키지
---

# \[Bridge] 도착 체인 안전 가드

목적지 체인이 허용 목록 안에 있는지, 도착 체인에서 실행되는 Callback이 포함되는지 확인해주는 패키지 입니다.

### 이 패키지로 막을 수 있는 것

* 도착 주소가 EVM이 아닌 체인(예: Solana)이라면 경고합니다.
* 도착 체인이 직접 등록한 허용 목록에 포함되지 않으면 차단합니다.
* 도착 체인에서 실행될 Callback이 있다면 경고합니다.

### 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-003.md" %}
[bridge-003.md](../../../standard-policies/market-offered-policies/bridge/bridge-003.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-004.md" %}
[bridge-004.md](../../../standard-policies/market-offered-policies/bridge/bridge-004.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-005.md" %}
[bridge-005.md](../../../standard-policies/market-offered-policies/bridge/bridge-005.md)
{% endcontent-ref %}

***

\[Bridge] 도착 체인 안전 가드\
Wallet Guardians | v.1.0.0 | 26/06/14\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
