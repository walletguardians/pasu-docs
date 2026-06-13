---
description: Restaking Deposit into a Non-Allowlisted Strategy (WARN)
---

# RESTAKE-003: 허용 목록에 없는 스트래티지로 리스테이크를 예치할 시 경고

### Policy Definition (정책 정의)

> EigenLayer에 리스테이크를 예치할 때, 예치하려는 **스트래티지(strategy)가 미리 정한 허용 목록에 없으면** 대상을 다시 확인하도록 경고합니다.

EigenLayer 리스테이크는 토큰별로 나뉜 스트래티지(strategy) 컨트랙트에 자산을 넣는 방식입니다. 어떤 스트래티지에 예치하느냐에 따라 지분이 묶이는 곳과 슬래싱(slashing) 위험이 달라지므로, 평소 쓰던 스트래티지가 아니라면 주소가 바뀌었거나 엉뚱한 컨트랙트로 유도됐을 수 있습니다. 그래서 예치 대상 스트래티지가 허용 목록에 없으면, 정말 맞는 곳에 넣는지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

EigenLayer에 리스테이크를 예치하는 경우에 적용됩니다. 예치 대상 스트래티지가 허용 목록에 들어 있지 않을 때만 발동합니다. (Deposit)

#### Audience (대상 사용자)

EigenLayer에 직접 리스테이크를 예치하는 사용자

#### Used Data (판정에 사용될 데이터)

예치 대상 스트래티지 주소(`context.strategy`)가 정책에 적어둔 허용 목록에 들어 있는지 여부입니다. 외부 조회 없이 정책 안에 박힌 목록과 대조해 판정합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("deposit-strategy-not-allowlisted-warn")
@severity("warn")
@reason("허용 목록에 없는 스트래티지로 리스테이크 예치합니다 — 대상 스트래티지를 확인하세요")
forbid (
    principal,
    action == Restaking::Action::"Deposit",
    resource
)
when
{
    !(["0x0000000000000000000000000000000000000000"].contains(context.strategy))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "deposit-strategy-not-allowlisted-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "deposit" } } }
}
```
{% endcode %}

***

**RESTAKE-003: 허용 목록에 없는 스트래티지로 리스테이크를 예치할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
