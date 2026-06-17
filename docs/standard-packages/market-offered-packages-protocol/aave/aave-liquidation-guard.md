---
description: Aave에서 HF 관리를 위한 정책 패키지
---

# \[Aave] Liquidation Guard

담보를 넣고 빌리면 청산 여유(HF)가 곧 생명선인데, 무리한 차입·담보 출금·담보 해제·이모드 변경처럼 그 선을 스스로 깎는 동작을 정책적으로 관리할 수 있도록 구성한 패키지입니다.

(HF 변동 정도는 지갑의 현재 포지션을 읽어 이번 동작을 반영한 보수적 추정으로 따집니다.)

## 이 패키지로 막을 수 있는 것

* 차입 직후 청산 여유(health factor)가 위험 수준(1.5 미만)으로 떨어지는 경우 경고합니다.
* 담보 출금으로 청산 직전까지 몰리는 경우 경고합니다.
* 빚이 있는데 담보 사용을 끄는 ㅈ경우 경고합니다.
* 빚이 열린 상태에서 청산 기준을 바꾸는 이모드(e-mode) 변경을 경고합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/lending/lend-011.md" %}
[lend-011.md](../../../standard-policies/market-offered-policies/lending/lend-011.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/lending/lend-012.md" %}
[lend-012.md](../../../standard-policies/market-offered-policies/lending/lend-012.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/lending/lend-013.md" %}
[lend-013.md](../../../standard-policies/market-offered-policies/lending/lend-013.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/lending/lend-014.md" %}
[lend-014.md](../../../standard-policies/market-offered-policies/lending/lend-014.md)
{% endcontent-ref %}

***

\[Aave] Liquidation Guard\
Wallet Guardians | v.1.0.0 | 26/06/17\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
