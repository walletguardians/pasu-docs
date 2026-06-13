---
description: Pay-to-Claim Donation Above the Cap (WARN)
---

# AIRDROP-008: 클레임에 내는 결제 금액이 한도를 넘을 시 경고

### Policy Definition (정책 정의)

> 에어드롭을 받는 데 **결제(donation)가 필요한데** 그 금액이 정한 한도를 넘으면 경고합니다.

대개 에어드롭은 가스비만 내고 받지만, 일부 배포 컨트랙트는 클레임할 때 별도의 결제(donation)를 요구합니다. LayerZero의 `donateAndClaim`이 그런 방식으로, 이때 낸 돈은 환불되지 않습니다. 문제는 클레임 페이지가 변조되면 이 결제 금액을 몰래 부풀려 더 많이 빼낼 수 있다는 점입니다. 받을 토큰은 그대로인데 나가는 돈만 커지는 셈입니다. 그래서 클레임에 따라붙는 결제 금액이 정한 한도를 넘으면, 내는 돈이 맞는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭 클레임에 적용됩니다. 클레임에 결제(donation)가 따라붙고, 그 금액이 설정 한도(기본 1e12 nano — 스테이블코인이면 약 $1,000, 네이티브 토큰이면 약 1,000개)를 넘는 경우입니다. (Claim)

#### Audience (대상 사용자)

토큰 에어드롭을 직접 클레임하는 사용자

#### Used Data (판정에 사용될 데이터)

클레임에 결제가 필요한지 여부와 그 결제 금액 (`context.donation.amountNano` — 클레임 calldata에서 디코딩한 값). 이 값을 설정 한도(`donationCapNano`, 기본 `1000000000000`)와 비교합니다. 스테이블코인 결제라면 1,000,000,000,000 nano가 약 $1,000, 네이티브 토큰이라면 약 1,000개에 해당합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-donation-amount-cap-warn")
@severity("warn")
@reason("이 에어드롭은 클레임에 결제가 필요한데 금액이 설정 한도를 넘습니다 — 변조된 클레임 페이지가 환불 불가 금액을 부풀릴 수 있으니 내는 금액을 확인하세요")
forbid (
    principal,
    action == Airdrop::Action::"Claim",
    resource
)
when
{
    context has donation &&
    context.donation has amountNano &&
    context.donation.amountNano > 1000000000000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-donation-amount-cap-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "airdrop" },
      "action.tag": { "eq": "claim" }
    }
  },
  "_template": {
    "donationCapNano": {
      "cedar_literal": "1000000000000",
      "default": "1000000000000",
      "meaning": "Maximum donation/fee you'll pay to claim, in token-native nano (1 token unit = 1e9 nano). For a stablecoin donation (USDC/USDT) nano ≈ USD, so 1000000000000 = $1,000; for a native-currency donation 1e9 nano = 1 native token. Tune to your own ceiling. The donation amount is the non-refundable payment a pay-to-claim distributor (e.g. LayerZero donateAndClaim) charges — this caps fat-finger and frontend-inflated over-payments. Dormant when the claim charges no donation or the donation token's decimals can't be resolved."
    }
  }
}
```
{% endcode %}

***

**AIRDROP-008: 클레임에 내는 결제 금액이 한도를 넘을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
