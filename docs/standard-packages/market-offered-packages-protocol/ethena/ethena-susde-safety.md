---
description: Ethena에서 stake, unstake 관리를 위한 패키지
---

# \[Ethena] sUSDe Safety

USDe를 sUSDe 볼트(ERC4626)에 맡기고(stake) 되찾는(unstake) 과정에서, 승인 및 인출을 안전하게 할 수 있도록 구성한 패키지입니다.

## 이 패키지로 막을 수 있는 것

* 악성으로 분류된 대상에게 USDe 사용을 승인하는 경우 차단합니다.
* 무제한 USDe approve를 경고합니다.
* 예치한 sUSDe 지분이 내 지갑이 아닌 주소로 발행되는 경우 차단합니다.
* 인출한 USDe가 내 지갑이 아닌 주소로 가는 경우 차단합니다.

## 정책 목록

{% content-ref url="../../../standard-policies/built-in-policies/token-001.md" %}
[token-001.md](../../../standard-policies/built-in-policies/token-001.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/token-erc-20/token-003.md" %}
[token-003.md](../../../standard-policies/market-offered-policies/token-erc-20/token-003.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-013.md" %}
[stake-013.md](../../../standard-policies/market-offered-policies/staking/stake-013.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/staking/stake-014.md" %}
[stake-014.md](../../../standard-policies/market-offered-policies/staking/stake-014.md)
{% endcontent-ref %}

***

\[Ethena] sUSDe Safety\
Wallet Guardians | v.1.0.0 | 26/06/17\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
