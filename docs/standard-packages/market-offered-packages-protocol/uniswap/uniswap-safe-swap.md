---
description: Uniswap에서 안전한 swap을 위한 정책 패키지
---

# \[Uniswap] Safe Swap

받을 주소가 잘못 되었거나, 슬리피지가 무방비이거나, 사려는 토큰이 스캠이거나 너무 얕은 풀이라 제값에 못 사는 흔한 사고를 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 받을 토큰이 내가 아닌 다른 주소로 가도록 설정된 Swap을 경고합니다.
* 받을 최소 수량이 0인 잘못된 Swap을 경고합니다.
* 슬리피지 허용치를 기준보다 크게 잡은 Swap을 경고합니다.
* exact-output Swap에서 낼 수 있는 최대 입력이 무제한인 경우 경고합니다.
* 악성으로 분류됐거나 검증 안 된 토큰을 사는 경우 경고합니다.
* 시가총액(FDV)이 극단적으로 작은 토큰을 사는 경우 경고합니다.
* 전체 유동성이 거의 없는 토큰을 사는 경우 경고합니다.



## 정책 목록

{% content-ref url="../../../standard-policies/built-in-policies/amm-001-swap-3.md" %}
[amm-001-swap-3.md](../../../standard-policies/built-in-policies/amm-001-swap-3.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-005-swap-exact-input.md" %}
[amm-005-swap-exact-input.md](../../../standard-policies/market-offered-policies/dex-amm/amm-005-swap-exact-input.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-010.md" %}
[amm-010.md](../../../standard-policies/market-offered-policies/dex-amm/amm-010.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-011.md" %}
[amm-011.md](../../../standard-policies/market-offered-policies/dex-amm/amm-011.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-012.md" %}
[amm-012.md](../../../standard-policies/market-offered-policies/dex-amm/amm-012.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/dex-amm/amm-015-fdv.md" %}
[amm-015-fdv.md](../../../readme-1/market-offered-policies/dex-amm/amm-015-fdv.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/dex-amm/amm-016.md" %}
[amm-016.md](../../../readme-1/market-offered-policies/dex-amm/amm-016.md)
{% endcontent-ref %}

***

\[Uniswap] Safe Swap\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
