---
description: stETH Share Transfer to a Non-Self Address (WARN)
---

# LIDO-005: stETH 지분(shares)을 본인이 아닌 주소로 전송할 시 경고

### Policy Definition (정책 정의)

> stETH의 **지분(shares)을 본인 지갑이 아닌 다른 주소로** 전송하려는 경우 경고합니다.

stETH는 잔액 숫자가 그대로 머무는 보통 토큰과 달리, 내부적으로 **지분(shares)** 단위로 보유량을 기록하고 리워드가 쌓일 때마다 잔액이 늘어나는 리베이스 토큰입니다. `transferShares`는 이 지분을 직접 옮기는 전송으로, 보내는 순간 그만큼의 stETH 소유권이 받는 주소로 넘어갑니다. 그래서 주소를 잘못 적거나 주소 오염(address poisoning)에 속아 엉뚱한 곳으로 보내면 그 지분은 되돌릴 수 없습니다. 받는 주소가 본인 지갑이 아니면 정말 보낼 주소가 맞는지 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

Lido에서 stETH 지분을 옮기는 전송에 적용됩니다. 받는 주소가 서명하는 본인 지갑과 다를 때 발동합니다. (TransferShares)

#### Audience (대상 사용자)

stETH를 보유하고 지분을 직접 전송하는 Lido 스테이커

#### Used Data (판정에 사용될 데이터)

지분을 받을 주소(`context.recipient`)와 서명하는 본인 지갑 주소(`principal.address`), 그리고 거래가 일어나는 venue가 Lido인지(`context.venue.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-shares-recipient-not-self-warn")
@severity("warn")
@reason("이 stETH(shares) 전송이 본인 지갑이 아닌 주소로 갑니다 — 받는 주소를 확인하세요")
forbid (
    principal,
    action == LiquidStaking::Action::"TransferShares",
    resource
)
when { context.venue.name == "lido" && context.recipient != principal.address };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-shares-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "transfer_shares" }
    }
  },
  "_template": {
    "trustedRecipients": { "cedar_extension": "&& !([\"0x...\"].contains(context.recipient))", "default": "[] (not applied)", "meaning": "optional allowlist of non-self recipients you trust; add to the cedar guard to suppress the warn for them" }
  }
}
```
{% endcode %}

***

**LIDO-005: stETH 지분(shares)을 본인이 아닌 주소로 전송할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
