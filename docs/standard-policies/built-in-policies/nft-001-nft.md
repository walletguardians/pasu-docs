---
description: Uncurated Marketplace Requesting NFT Collection Delegation (WARN)
---

# NFT-001: 선별되지 않은 마켓플레이스 컨트랙트의 NFT 컬렉션 위임 요청 시 경고

### Policy Definition (정책 정의)

> **선별되지 않은 NFT 마켓플레이스 컨트랙트**가 NFT 컬렉션에 대한 전체 승인(setApprovalForAll)을 요청하는 경우, 사용자가 승인 상대를 확인할 수 있도록 **경고**합니다.

정상적인 NFT 마켓플레이스인 것처럼 위장한 가짜 피싱 사이트에 NFT를 탈취당하는 가장 일반적인 메커니즘은 'NFT 컬렉션 판매'를 할 때 서명을 요청받는 setApprovalForAll에 서명하는 것입니다.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

그러나 컬렉션에 대한 권한을 위임받는 마켓플레이스가 피싱 사이트라면, 판매를 시도한 NFT 컬렉션 전체의 권한이 해커에게 탈취됩니다. 따라서 사용자가 사용하는 마켓플레이스가 OpenSea, Blur, LooksRare 중 하나에 해당하지 않는다면, 거래 상대를 재차 검토할 수 있도록 경고를 표시합니다.

#### Scope (적용 범위)

보유한 NFT 컬렉션의 전체 권한을 위임하는 서명 요청을 받았을 때 (setApprovalForAll)

#### Audience[^1] (대상 사용자)

NFT 트레이더, NFT 매매에 관심이 많은 사용자

#### Used Data (판정에 사용될 데이터)&#x20;

NFT 컬렉션에 대한 권한을 받을 사용자(`context.spender`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
// Day-1 Safety — NFT 컬렉션 전체 승인(setApprovalForAll) 경고 (순수 Cedar).
// 알려진 마켓플레이스 operator(OpenSea/Blur/LooksRare)는 allowlist 제외 — 그 밖 operator에게 grant(true) 할 때만 경고. operator = context.spender. revoke(false)는 통과.
@id("nft-set-approval-for-all-warn")
@severity("warn")
@reason("잘 알려지지 않은 NFT 마켓플레이스로 NFT 권한이 위임됩니다. 신뢰할 수 있는 거래소인지 검토하세요.")
forbid(principal, action == Token::Action::"NftSetApprovalForAll", resource)
when {
  context.approved == true
  && !([
    "0x1e0049783f008a0085193e00003d00cd54003c71", // OpenSea Seaport conduit
    "0x00000000000111abe46ff893f3b2fdf1f759a8a8", // Blur ExecutionDelegate
    "0x000000000060c4ca14cfc4325359062ace33fe3d"  // LooksRare TransferManager
  ].contains(context.spender))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "nft-set-approval-for-all-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "nft_set_approval_for_all"
      }
    }
  }
}
```
{% endcode %}

***

NFT-001: 선별되지 않은 마켓플레이스 컨트랙트의 NFT 컬렉션 위임 요청 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/09\
\
&#xNAN;_&#x53;upported Chain: Ethereum_

[^1]: 
