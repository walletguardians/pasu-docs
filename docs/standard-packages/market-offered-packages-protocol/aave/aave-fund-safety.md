---
description: Aave에서 안전한 자금 관리를 위한 정책 패키지
---

# \[Aave] Fund Safety

예치하려면 토큰 사용을 승인해야 하고 출금 및 예치는 받는 주소가 정해집니다. 승인 대상이 믿을 수 있는 대상에게만 가는지 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 악성으로 분류된 대상에게 토큰 사용을 승인하는 경우 차단합니다.
* 무제한 approve를 경고합니다(Aave Pool 등 정상 대상은 통과).
* 출금 자산이 내 지갑이 아닌 주소로 가는 경우 경고합니다.
* aToken 수령 주소가 내 지갑이 아닌 경우 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/built-in-policies/token-001.md" %}
[token-001.md](../../../standard-policies/built-in-policies/token-001.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-003.md" %}
[token-003.md](../../../standard-policies/market-offered-policies/token-erc-20/token-003.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/lending/lend-002-1.md" %}
[lend-002-1.md](../../../readme-1/market-offered-policies/lending/lend-002-1.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/lending/lend-007.md" %}
[lend-007.md](../../../readme-1/market-offered-policies/lending/lend-007.md)
{% endcontent-ref %}

***

\[Aave] Fund Safety\
Wallet Guardians | v.1.0.0 | 26/06/17\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
