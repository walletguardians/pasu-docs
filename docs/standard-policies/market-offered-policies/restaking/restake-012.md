---
description: Completing a Restaking Withdrawal to Another Address (WARN)
---

# \~\~RESTAKE-012: 출금 완료의 수령자가 본인이 아닐 시 경고\~\~ 필요없음

`RESTAKE-012` 출금 완료 수령자가 본인이 아닐 시 경고\
`completeQueuedWithdrawal(s)` 자체는 있지만 내부에서 `msg.sender == withdrawal.withdrawer`를 요구합니다. 즉 유효하게 성공하는 calldata에서는 `context.withdrawer != context.meta.submitter` 상황이 성립하지 않습니다. 이 정책은 revert 날 트랜잭션을 미리 경고하는 정도입니다. EigenLayer source: [DelegationManager.sol](https://github.com/Layr-Labs/eigenlayer-contracts/blob/master/src/contracts/core/DelegationManager.sol#L535-L542).\
반대로 `RESTAKE-013(receiveAsTokens=false)`는 실제 bool 파라미터가 있고 의미 있습니다: \[complete withdrawal schema (line 17)]\(/Users/woojin/Desktop/upside\_academy/project/policy-engine/schema/policy-schema/actions/restaking/complete\_withdrawal.cedarschema:17).

###

###

###

### Policy Definition (정책 정의)

> EigenLayer 출금을 마무리할 때 자산을 받는 주소(**withdrawer**)가 트랜잭션을 보내는 본인이 아니면, 서명 전에 한 번 더 확인하도록 경고합니다.

EigenLayer에서 리스테이크한 지분을 빼낼 때는 먼저 출금을 예약하고(Queue Withdrawal), 정해진 대기 기간이 지난 뒤 출금을 마무리(Complete Withdrawal)합니다. 이때 빠져나온 자산이나 지분은 보통 출금을 진행하는 본인 지갑으로 들어옵니다. 그런데 이 마무리 단계에서 수령자가 본인이 아닌 다른 주소로 잡혀 있다면, 회수한 자산이 그대로 남에게 흘러갈 수 있습니다. 게다가 지금 메인넷에서는 출금을 다른 주소로 보내는 형태 자체가 흔치 않아, 이런 값이 들어오면 정상 흐름이라기보다 버그가 있는 프론트엔드나 비표준 배포를 거쳤을 가능성이 큽니다. 그래서 수령자가 본인과 다르면 서명 전에 경고를 띄워 진짜 의도한 주소가 맞는지 확인하게 합니다.

#### Scope (적용 범위)

EigenLayer에서 예약해 둔 출금을 마무리하는 경우에 적용됩니다. 자산을 받는 주소가 트랜잭션을 보내는 본인과 다를 때만 발동합니다. (Complete Withdrawal)

#### Audience (대상 사용자)

EigenLayer에 자산을 리스테이크하고 직접 출금을 마무리하는 사용자

#### Used Data (판정에 사용될 데이터)

출금 자산을 받도록 지정된 수령자 주소(`context.withdrawer`)와, 이 트랜잭션을 실제로 보내는 본인 주소(`context.meta.submitter`)를 비교합니다. 두 주소가 다를 때 경고합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("complete-withdrawal-to-other-warn")
@severity("warn")
@reason("출금 완료의 수령자(withdrawer)가 본인이 아닙니다 — 현재 메인넷에서는 이례적인 형태이니 버그 프론트엔드나 비표준 배포가 아닌지 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"CompleteWithdrawal",
    resource
)
when { context.withdrawer != context.meta.submitter };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "complete-withdrawal-to-other-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "complete_withdrawal" } } }
}
```
{% endcode %}

***

**RESTAKE-012: 출금 완료의 수령자가 본인이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
