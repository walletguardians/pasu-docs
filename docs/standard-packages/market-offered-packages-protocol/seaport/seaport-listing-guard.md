---
description: Seaport에서 토큰 판매를 관리하기 위한 패키지
---

# \[Seaport] Listing Guard

OpenSea 등 Seaport 마켓에서 NFT를 팔거나 오퍼 및 입찰에 서명할 때, 그 서명 한 번에 컬렉션 전체가 넘어가거나 지나치게 낮은 가격에 등록 및 낯선 운영자(conduit)에게 권한을 옮기는 것을 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 컬렉션 전체를 내주는 서명을 차단합니다.
* 대금이 내 주소로 한 푼도 들어오지 않는 주문을 경고합니다.
* Seaport 주문 타입이 아닌 주문의 서명을 차단합니다.
* 유효기간이 비정상적으로 긴 주문을 경고합니다.
* 받을 대금이 컬렉션 최저가보다 현저히 낮은 리스팅을 경고합니다.
* 토큰을 낯선 운영자(conduit)로 옮기는 주문을 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-007.md" %}
[nft-007.md](../../../standard-policies/market-offered-policies/nft/nft-007.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-008.md" %}
[nft-008.md](../../../standard-policies/market-offered-policies/nft/nft-008.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-009.md" %}
[nft-009.md](../../../standard-policies/market-offered-policies/nft/nft-009.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-010.md" %}
[nft-010.md](../../../standard-policies/market-offered-policies/nft/nft-010.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-011.md" %}
[nft-011.md](../../../standard-policies/market-offered-policies/nft/nft-011.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-012.md" %}
[nft-012.md](../../../standard-policies/market-offered-policies/nft/nft-012.md)
{% endcontent-ref %}

***

\[Seaport] Listing Guard\
Wallet Guardians | v.1.0.0 | 26/06/17\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
