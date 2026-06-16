---
description: Uniswap에서 안전한 LP를 위한 정책 패키지
---

# \[Uniswap] Safe LP

포지션, 수수료, 출금 자산을 받는 주소가 내가 아니면 경고하고, 거래도 거의 없고 묶인 자금도 얕아 빠져나오기 힘든 풀에 유동성에 공급하는 활동을 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 24시간 거래량이 거의 없는 풀에 유동성을 넣을 때 경고합니다.
* 잠긴 유동성(TVL)이 너무 얕은 풀에 유동성을 넣을 때 경고합니다.
* LP 포지션을 받을 주소가 내가 아닐 때 경고합니다.
* 모은 수수료를 받을 주소가 내가 아닐 때 경고합니다.
* 유동성을 빼면서 자산이 제3자에게 가는 경우 차단합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-002-lp-lp-token-nft-3.md" %}
[amm-002-lp-lp-token-nft-3.md](../../../standard-policies/market-offered-policies/dex-amm/amm-002-lp-lp-token-nft-3.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-003-lp-3.md" %}
[amm-003-lp-3.md](../../../standard-policies/market-offered-policies/dex-amm/amm-003-lp-3.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-004-lp-3.md" %}
[amm-004-lp-3.md](../../../standard-policies/market-offered-policies/dex-amm/amm-004-lp-3.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-006.md" %}
[amm-006.md](../../../standard-policies/market-offered-policies/dex-amm/amm-006.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/dex-amm/amm-014-tvl.md" %}
[amm-014-tvl.md](../../../readme-1/market-offered-policies/dex-amm/amm-014-tvl.md)
{% endcontent-ref %}

***

\[Uniswap] Safe LP\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
