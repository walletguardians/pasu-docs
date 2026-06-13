---
description: Queuing a Restaking Withdrawal to Someone Other Than Yourself (WARN)
---

# \~\~RESTAKE-011: 출금 예약의 수령자가 본인이 아닐 시 경고\~\~

`RESTAKE-011` 출금 예약 수령자 경고\
EigenLayer `queueWithdrawals`에는 `__deprecated_withdrawer` 필드가 ABI에 남아 있지만, 현재 구현은 이 값을 쓰지 않고 `withdrawer: staker`로 큐를 만듭니다. 우리 manifest는 아직 그 deprecated input을 `context.withdrawer`로 emit합니다: \[queueWithdrawals manifest (line 31)]\(/Users/woojin/Desktop/upside\_academy/project/policy-engine/registryV2/manifests/eigenlayer/delegation-manager/queueWithdrawals@1.0.0.json:31).\
그래서 “다른 수령자로 출금 예약”은 실제 자금 흐름상 존재하지 않습니다. 버그 프론트엔드 탐지용 경고로는 가능하지만, 보안 정책으로는 약합니다. EigenLayer source: [DelegationManager.sol](https://github.com/Layr-Labs/eigenlayer-contracts/blob/master/src/contracts/core/DelegationManager.sol#L176-L200), [withdrawer set to staker](https://github.com/Layr-Labs/eigenlayer-contracts/blob/master/src/contracts/core/DelegationManager.sol#L430-L525).







### Policy Definition (정책 정의)

> EigenLayer 출금을 예약할 때 받는 사람(**withdrawer**)이 서명한 본인이 아니면, 비정상적인 신호일 수 있어 서명 전에 다시 확인하도록 경고합니다.

EigenLayer에서 리스테이킹한 지분을 빼려면 먼저 출금을 예약(QueueWithdrawal)하고, 일정 기간이 지난 뒤 실제로 받아갑니다. 이때 예약 요청에는 나중에 자산을 받을 주소(withdrawer)가 담기는데, 정상적인 흐름이라면 이 값은 출금을 요청한 본인입니다. 지금 메인넷 컨트랙트는 이 필드를 사실상 무시하고 요청자 본인을 수령자로 강제하기 때문에, 출금을 예약하면서 받는 사람을 남으로 지정한 요청은 메인넷에서 거의 보이지 않습니다. 그래서 이런 요청이 잡히면 버그가 있는 프론트엔드나 표준에서 벗어난 배포를 의심할 만한 신호로 보고, 서명하기 전에 한 번 멈춰 확인하도록 알립니다.

#### Scope (적용 범위)

EigenLayer에서 출금을 예약하는 경우에 적용됩니다. 예약 요청의 수령자(withdrawer)가 서명한 본인과 다를 때만 발동합니다. (Queue Withdrawal)

#### Audience (대상 사용자)

EigenLayer에 리스테이킹한 지분을 출금하려는 사용자

#### Used Data (판정에 사용될 데이터)

출금 예약에 담긴 수령자 주소(`context.withdrawer`)와 이번 요청에 서명한 본인 주소(`context.meta.submitter`)를 비교합니다. 둘이 다르면 발동하며, 별도의 외부 조회 없이 디코드한 값만으로 판정합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("queue-withdrawal-to-other-warn")
@severity("warn")
@reason("출금 예약의 수령자(withdrawer)가 본인이 아닙니다 — 현재 메인넷에서는 이례적인 형태이니(해당 필드는 무시됨) 버그 프론트엔드나 비표준 배포가 아닌지 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"QueueWithdrawal",
    resource
)
when { context.withdrawer != context.meta.submitter };
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "queue-withdrawal-to-other-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "queue_withdrawal" } } }
}
```
{% endcode %}

***

**RESTAKE-011: 출금 예약의 수령자가 본인이 아닐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
