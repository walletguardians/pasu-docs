---
description: Launchpad Refund Sent to a Third Party (WARN)
---

# LAUNCH-005: 환불 수령처가 본인 지갑이 아닐 시 경고

### Policy Definition (정책 정의)

> 세일에서 받는 **환불의 수령처가 본인 지갑이 아닌** 경우, 받는 주소를 확인하도록 경고합니다.

Launchpad 세일에 참여하면 보낸 자금이 그대로 배정으로 이어지지 않을 때가 있습니다. 한도를 넘겨 commit 한 금액이나, 세일이 무산돼 돌려받는 몫이 환불로 나옵니다. 보통 이 환불은 처음 자금을 낸 본인 지갑으로 돌아오는 게 자연스럽습니다. 그런데 환불 트랜잭션에 적힌 받는 주소가 본인 지갑과 다르면, 돌려받아야 할 돈이 남의 지갑으로 빠져나갑니다. 받는 주소를 바꿔치기하는 피싱이거나 입력 실수일 수 있습니다. 따라서 환불 수령처가 서명하는 본인 지갑과 다르면, 보내기 전에 경고합니다.

#### Scope (적용 범위)

Launchpad 환불. 환불을 받는 주소가 트랜잭션에 서명하는 본인 지갑과 다른 경우에 적용됩니다. (Refund)

#### Audience (대상 사용자)

토큰 세일(IDO)에 참여해 commit·환불을 주고받는 사용자

#### Used Data (판정에 사용될 데이터)

환불을 받는 주소(`context.recipient`)와 서명하는 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("refund-recipient-not-self-warn")
@severity("warn")
@reason("환불 수령처가 본인 지갑이 아닙니다 — 받는 주소를 확인하세요")
forbid(principal, action == Launchpad::Action::"Refund", resource)
when {
  context.recipient != principal.address
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

**LAUNCH-005: 환불 수령처가 본인 지갑이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
