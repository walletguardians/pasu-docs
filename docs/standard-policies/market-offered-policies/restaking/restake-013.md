---
description: Completing a Restaking Withdrawal as Shares Instead of Tokens (WARN)
---

# RESTAKE-013: 출금을 토큰이 아닌 지분으로 받을 시 경고

### Policy Definition (정책 정의)

> EigenLayer에서 큐에 넣어둔 출금을 마무리할 때, 받는 형태가 **토큰이 아니라 지분(shares)**이면 한 번 더 확인하도록 경고합니다.

EigenLayer 출금은 두 단계입니다. 먼저 `QueueWithdrawal`로 출금을 예약해 대기 기간을 채우고, 그다음 `CompleteWithdrawal`로 실제로 받아냅니다. 이 마지막 단계에서 `receiveAsTokens` 값을 고를 수 있는데, `true`면 지갑으로 토큰이 들어오고, `false`면 토큰 대신 리스테이크 지분으로 받습니다. 지분으로 받으면 자산이 지갑으로 빠져나오는 게 아니라 지금 위임 중인 오퍼레이터에게 다시 예치·위임됩니다. 출금을 마무리한다고 생각했는데 실제로는 자금이 그대로 묶여 있고, 오퍼레이터 교체나 위임 해제를 끝내려던 의도와 어긋날 수 있습니다. 그래서 `receiveAsTokens`가 `false`로 들어오면 정말 지분으로 받으려는 게 맞는지 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

EigenLayer에서 대기 중인 출금을 마무리하는 동작에 적용됩니다. 받는 형태가 지분으로 지정된 경우(`receiveAsTokens == false`)에만 발동합니다. (Complete Withdrawal)

#### Audience (대상 사용자)

EigenLayer에 리스테이크한 자산을 빼거나 오퍼레이터를 정리하려는 사용자

#### Used Data (판정에 사용될 데이터)

출금을 토큰으로 받는지 지분으로 받는지 가리키는 값(`context.receiveAsTokens`). 이 값은 출금 완료 calldata에서 그대로 읽어옵니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("complete-withdrawal-receive-as-shares-warn")
@severity("warn")
@reason("출금을 토큰 대신 지분(shares)으로 완료합니다(receiveAsTokens=false) — 토큰 인출 대신 현재 오퍼레이터에게 다시 예치·위임되니 지분 수령이 맞는지 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"CompleteWithdrawal",
    resource
)
when { context has receiveAsTokens && context.receiveAsTokens == false };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "complete-withdrawal-receive-as-shares-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "complete_withdrawal" } } }
}
```
{% endcode %}

***

**RESTAKE-013: 출금을 토큰이 아닌 지분으로 받을 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
