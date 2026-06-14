---
description: Completing a Restaking Withdrawal as Shares Instead of Tokens (WARN)
---

# RESTAKE-013: 출금을 토큰이 아닌 지분으로 받을 시 경고

### Policy Definition (정책 정의)

> EigenLayer에서 큐에 넣어둔 출금을 마무리할 때, 토큰이 아니라 지분(shares) 형태로 받으면 한 번 더 확인하도록 경고합니다.

EigenLayer에서 출금할 때에는 토큰으로 받을 수도, 리스테이크 지분으로 받을 수도 있습니다. 지분으로 받으면 자산이 지갑으로 빠져나오는 게 아니라 지금 위임 중인 오퍼레이터에게 다시 예치·위임됩니다.

이 정책은 출금할 때 토큰이 아니라 지분으로 받게 되는 경우 서명 전에 한 번 더 확인하도록 알립니다.

#### Scope (적용 범위)

EigenLayer에서 대기 중인 출금을 마무리하는 동작에 적용됩니다.

#### Audience (대상 사용자)

EigenLayer에 리스테이크한 자산을 빼거나 오퍼레이터를 정리하려는 사용자

#### Used Data (판정에 사용될 데이터)

* 출금을 토큰으로 받는지 지분으로 받는지 가리키는 값(`context.receiveAsTokens`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("complete-withdrawal-receive-as-shares-warn")
@severity("warn")
@reason("지금 출금은 지갑으로 토큰이 입금되는 출금이 아니라, 오퍼레이터에게 다시 예치·위임되는 출금입니다.")
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
\&#xNAN;_Supported Chain: Ethereum_
