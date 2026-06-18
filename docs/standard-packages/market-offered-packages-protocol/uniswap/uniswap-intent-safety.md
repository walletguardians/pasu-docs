---
description: Uniswap에서 안전한 Intent 거래를 위한 정책 패키지
---

# \[Uniswap] Intent Safety

UniswapX는 내가 직접 Swap을 보내는 대신 "이런 조건이면 체결해 달라"는 주문을 서명하면 솔버가 대신 체결합니다. 서명을 가로채는 화면이 받을 주소를 바꿔치기하거나, 기한이 너무 길게 열린 주문이 가격이 틀어진 뒤 체결되는 경우를 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 받을 토큰이 내가 아닌 다른 주소로 가는 주문을 경고합니다.
* 체결 기한(TTL)이 일주일을 넘게 열려 있는 주문을 경고합니다.
* 악성으로 분류됐거나 검증 안 된 토큰을 사는 주문을 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/dex-amm/amm-012.md" %}
[amm-012.md](../../../standard-policies/market-offered-policies/dex-amm/amm-012.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/dex-amm/amm-017.md" %}
[amm-017.md](../../../readme-1/market-offered-policies/dex-amm/amm-017.md)
{% endcontent-ref %}

{% content-ref url="../../../readme-1/market-offered-policies/dex-amm/amm-018.md" %}
[amm-018.md](../../../readme-1/market-offered-policies/dex-amm/amm-018.md)
{% endcontent-ref %}

***

\[Uniswap] Intent Guard\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
