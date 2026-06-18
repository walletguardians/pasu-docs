---
description: Launchpad Refund Sent to a Third Party (WARN)
---

# LAUNCH-004: 환불 수령처가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고

### Policy Definition (정책 정의)

> 세일에서 받는 **환불의 수령처가 본인 지갑이 아닌** 경우, 받는 주소를 확인하도록 경고합니다.

Launchpad 세일에 참여하면 보낸 자금이 그대로 배정으로 이어지지 않을 때가 있습니다. 한도를 넘겨 commit 한 금액이나, 세일이 무산돼 돌려받는 몫이 환불로 나옵니다. 환불 트랜잭션에 적힌 받는 주소가 잘못 설정되면, 돌려받아야 할 돈이 남의 지갑으로 빠져나갑니다. 환불 수령처가 서명하는 본인 지갑과 다르고 승인 목록에도 없는 경우 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

환불을 받는 수령처가 승인 목록에 없는 경우 적용됩니다.

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 환불을 요청하는 사용자

#### Used Data (판정에 사용될 데이터)

환불을 받는 주소(`context.recipient`)가 서명하는 지갑 주소(`principal.address`)와 같거나 미리 등록해 둔 승인 목록에 포함되는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("refund-recipient-not-self-warn")
@severity("warn")
@reason("환불 수령처가 승인 목록에 없는 주소입니다 — 수령 주소를 확인하세요")
forbid(principal, action == Launchpad::Action::"Refund", resource)
when {
    context.recipient != principal.address &&
    !["0x1111111111111111111111111111111111111111"].contains
        (
            context.recipient
        )  
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "refund-recipient-not-self-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.tag": {
        "eq": "refund"
      }
    }
  }
}
```
{% endcode %}

***

**LAUNCH-004:** 환불 수령처가 본인 지갑이 아니고 승인 목록에도 없을 경우 경고\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
