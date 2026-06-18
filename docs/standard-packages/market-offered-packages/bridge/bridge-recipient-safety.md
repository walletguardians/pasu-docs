---
description: 자금이 잘못된 도착 주소로 가는 사고를 막는 패키지
---

# \[Bridge] 도착 주소 관리

브릿지는 보내는 체인과 받는 체인이 달라 도착 주소를 한 번 잘못 적으면 되돌릴 수 없습니다. 도착 주소가 차단 목록에 있는지, 본인 지갑이 맞는지, non EVM 체인인지 확인해서 잘못된 곳으로 도착하는 사고를 막는 패키지입니다.

### 이 패키지로 막을 수 있는 것

* 도착 주소가 소각 주소이거나 직접 등록한 차단 목록에 있으면 차단합니다.
* 도착 주소가 본인 지갑이 아니면 경고합니다.
* 도착 주소가 EVM이 아닌 체인(예: Solana)이라면 경고합니다.

### 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-001.md" %}
[bridge-001.md](../../../standard-policies/market-offered-policies/bridge/bridge-001.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-002.md" %}
[bridge-002.md](../../../standard-policies/market-offered-policies/bridge/bridge-002.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/bridge/bridge-003.md" %}
[bridge-003.md](../../../standard-policies/market-offered-policies/bridge/bridge-003.md)
{% endcontent-ref %}

***

\[Bridge] 도착 주소 관리\
Wallet Guardians | v.1.0.0 | 26/06/14\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
