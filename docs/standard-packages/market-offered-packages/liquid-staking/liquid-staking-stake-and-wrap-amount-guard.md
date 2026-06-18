# \[Liquid Staking] 출금 주소 가드

유동성 스테이킹(Liquid Staking)에서는 출금을 하는 경우, 출금 청구권을 받고 나중에 출금을 청구합니다. 이때, 출금 청구권을 받는 주소나 출금되는 주소가 올바른지 확인해야 합니다.

출금은 permit을 통해서 한번에 이루어질 수도 있는데, permit 요청이 무제한 출금이 가능하지 않은지도 확인해야 합니다.

### 이 패키지로 막을 수 있는 것

유동성 스테이킹에서 출금을 하는 경우, 출금 청구권과 출금 주소가 안전한지 검사합니다.

* Lido에서 출금 청구권이 본인이 아닌 주소로 발행될 시 차단합니다.
* Lido에서 출금 청구권이 소각 주소로 발행될 시 차단합니다.
* Lido에서 출금 청구 시 ETH를 받는 주소가 본인 주소가 아닌 경우 차단합니다.
* Lido에서 출금 청구 시 ETH를 받는 주소가 소각 주소인 경우 차단합니다.

또, permit 요청이 무제한 요청인 경우 경고합니다.

### 정책 목록

{% content-ref url="../../../standard-policies/market-offered-policies/liquid-staking/lido-007.md" %}
[lido-007.md](../../../standard-policies/market-offered-policies/liquid-staking/lido-007.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/liquid-staking/lido-008.md" %}
[lido-008.md](../../../standard-policies/market-offered-policies/liquid-staking/lido-008.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/liquid-staking/lido-010.md" %}
[lido-010.md](../../../standard-policies/market-offered-policies/liquid-staking/lido-010.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/liquid-staking/lido-011.md" %}
[lido-011.md](../../../standard-policies/market-offered-policies/liquid-staking/lido-011.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/liquid-staking/lido-009.md" %}
[lido-009.md](../../../standard-policies/market-offered-policies/liquid-staking/lido-009.md)
{% endcontent-ref %}

***

\[Liquid Staking] 출금 주소 가드\
Wallet Guardians | v.1.0.0 | 26/06/11\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
