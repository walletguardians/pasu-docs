---
description: Borrow Taken Out on Behalf of Another Account (WARN)
---

# LEND-004: 대출시에 빚이 본인이 아닌 다른 계정 앞으로 잡힐 시 경고

### Policy Definition (정책 정의)

> 빌린 빚이 트랜잭션을 보내는 **내 지갑이 아니라 다른 계정 앞으로** 잡히는 경우, 신용 위임이 맞는지 확인하도록 경고합니다.

Aave 같은 대출 프로토콜의 borrow는 빚을 누구 앞으로 잡을지 `onBehalfOf` 칸으로 따로 지정할 수 있습니다. 보통은 빌리는 사람과 빚을 지는 사람이 같지만, 신용 위임(credit delegation)을 쓰면 미리 한도를 위임받은 사람이 위임한 계정의 담보로 대신 빌릴 수 있습니다. 문제는 이 `onBehalfOf`가 다른 계정으로 바뀌어 있으면, 내 담보로 잡힌 빚이 통째로 남의 주소로 넘어가거나, 거꾸로 내가 모르는 빚을 떠안게 될 수 있다는 점입니다. 따라서 borrow의 대상 계정이 트랜잭션을 보내는 주소와 다르면, 의도한 신용 위임인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

대출 프로토콜의 차입(Borrow). 빚을 잡을 대상 계정이 트랜잭션을 보내는 주소와 다른 경우에 적용됩니다.

#### Audience (대상 사용자)

신용 위임으로 남의 한도를 빌리거나, 여러 계정을 오가며 대출을 다루는 사용자

#### Used Data (판정에 사용될 데이터)

* 빚을 잡을 대상 계정
* 트랜잭션을 보내는 주소

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("borrow-on-behalf-mismatch-warn")
@severity("warn")
@reason("이 대출이 본인이 아닌 다른 계정 앞으로 잡힙니다 — 신용 위임(credit delegation)이 맞는지 확인하세요")
forbid(principal, action == Lending::Action::"Borrow", resource)
when {
  context has onBehalfOf
  && context.onBehalfOf != context.meta.submitter
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "borrow-on-behalf-mismatch-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "borrow" } } }
}
```
{% endcode %}

***

**LEND-004: 대출이 본인이 아닌 다른 계정 앞으로 잡힐 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
