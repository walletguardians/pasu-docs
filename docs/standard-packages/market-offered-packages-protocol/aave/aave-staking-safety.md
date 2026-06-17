---
description: Aave에서 스테이킹 관리를 위한 정책 패키지
---

# \[Aave] Staking Guard

Safety Module에 맡기면 프로토콜에 부족분이 생겼을 때 그 자금이 슬래싱으로 빠질 수 있어 원금이 위험에 노출됩니다. 슬래싱 위험을 알리고, 스테이크, 언스테이크, 보상이 내 주소로 돌아오는지 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 스테이크가 본인이 아닌 주소로 귀속되는 경우 차단합니다.
* 언스테이크(redeem) 자산이 제3자에게 가는 경우 차단합니다.
* 보상 수령자가 본인이 아닌 경우 차단합니다.
* Safety Module 스테이킹이 슬래싱으로 원금 손실에 노출됨을 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-001.md" %}
[stake-001.md](../../../standard-policies/market-offered-policies/staking/stake-001.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-002.md" %}
[stake-002.md](../../../standard-policies/market-offered-policies/staking/stake-002.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-004.md" %}
[stake-004.md](../../../standard-policies/market-offered-policies/staking/stake-004.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-007.md" %}
[stake-007.md](../../../standard-policies/market-offered-policies/staking/stake-007.md)
{% endcontent-ref %}

***

\[Aave] Staking Guard\
Wallet Guardians | v.1.0.0 | 26/06/17\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
