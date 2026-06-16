---
description: Uniswap에서 안전한 Approval을 위한 정책 패키지
---

# \[Uniswap] Safe Approval

Uniswap은 Universal Router와 Permit2에 토큰 권한을 미리 위임받아 동작하는데, 이 권한이 무제한이거나 만료가 너무 길거나 엉뚱한 대상에게 가면 나중에 지갑을 통째로 털리는 사고를 정책적으로 관리할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 무제한 approve를 경고합니다(유니스왑 라우터·Permit2처럼 정상 패턴인 곳은 통과시킵니다).
* 악성으로 분류된 승인 대상(Spender)에게 주는 approve를 차단합니다.
* approve 금액이 기준을 넘으면 경고합니다.
* Permit2 한도가 무제한으로 서명되는 경우 경고합니다.
* Permit2 만료가 기준을 넘으면 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/built-in-policies/token-001.md" %}
[token-001.md](../../../standard-policies/built-in-policies/token-001.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-003.md" %}
[token-003.md](../../../standard-policies/market-offered-policies/token-erc-20/token-003.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-004.md" %}
[token-004.md](../../../standard-policies/market-offered-policies/token-erc-20/token-004.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-006.md" %}
[token-006.md](../../../standard-policies/market-offered-policies/token-erc-20/token-006.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-007.md" %}
[token-007.md](../../../standard-policies/market-offered-policies/token-erc-20/token-007.md)
{% endcontent-ref %}

***

\[Uniswap] Safe Approval\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
