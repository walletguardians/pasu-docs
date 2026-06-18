# \[NFT] NFT 컬렉션 권한 위임 보호

`setApprovalForAll`은 NFT 컬렉션 전체를 옮길 권한을 통째로 넘기는 동작입니다. 위임 대상이 NFT 마켓의 정식 conduit인지 검사해 사고를 막을 수 있습니다. 위임 관리를할 수 있도록 구성한 패키지입니다.

### 이 패키지로 막을 수 있는 것

* 사용자 허용 목록에 없는 주소에 컬렉션 전체 권한을 위임하는 상황
* 알려진 마켓 conduit가 아닌 곳에 컬렉션 전체 권한을 위임하는 상황
* 낯선 conduit로 토큰 권한을 경유시키는 서명을 하는 상황

### 정책 목록

{% content-ref url="../../../standard-policies/built-in-policies/nft-001-nft.md" %}
[nft-001-nft.md](../../../standard-policies/built-in-policies/nft-001-nft.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-002.md" %}
[nft-002.md](../../../standard-policies/market-offered-policies/nft/nft-002.md)
{% endcontent-ref %}

{% content-ref url="../../../standard-policies/market-offered-policies/nft/nft-007.md" %}
[nft-007.md](../../../standard-policies/market-offered-policies/nft/nft-007.md)
{% endcontent-ref %}

***

**\[NFT] Safe Collection Approval**\
Wallet Guardians | v.1.0.0 | 26/06/11\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
