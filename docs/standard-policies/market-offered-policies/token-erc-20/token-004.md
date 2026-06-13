---
description: Token Approval Above USD Cap (WARN)
---

# TOKEN-004: 승인 금액이 $10,000을 초과할 시 경고

### Policy Definition (정책 정의)

> 한 번의 승인으로 내주는 토큰 사용 권한이 미화 약 $10,000을 넘는 경우, 규모를 다시 확인하도록 경고합니다.

승인은 '지금 당장' 토큰을 옮기는 게 아니라, 상대가 나중에 가져갈 수 있는 권한을 미리 열어두는 일입니다. 그래서 승인 금액이 클수록, 훗날 그 상대가 해킹당하거나 마음을 바꿔 악용하려 들 때 한 번에 빠져나갈 수 있는 금액도 커집니다. 승인 규모를 시세로 환산한 값이 기준을 넘으면, 정말 이만한 권한을 열어줄 만한지 서명 전에 한 번 더 따져보도록 경고합니다.

#### Scope (적용 범위)

ERC-20 Approve 서명. 승인하는 토큰과 수량을 시세로 환산했을 때 $10,000을 넘는 경우에 적용됩니다.

#### Audience (대상 사용자)

여러 dApp을 오가며 승인을 자주 하는 사용자

#### Used Data (판정에 사용될 데이터)

승인하는 토큰과 수량의 USD 환산 가치 (`approveUsd` — 시세 조회 결과)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("approve-usd-cap-warn")
@severity("warn")
@reason("이번 승인 금액이 약 $10,000을 넘습니다. 승인 대상과 금액이 맞는지 확인하세요.")
forbid(principal, action == Token::Action::"Erc20Approve", resource)
when {
  context has custom
  && context.custom has approveUsd
  && context.custom.approveUsd.greaterThan(decimal("10000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "approve-usd-cap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "erc20_approve" } } },
  "policy_rpc": [{
    "id": "approve-usd",
    "method": "oracle.usd_value",
    "params": {
      "chain_id": "$.root.chain_id",
      "asset": "$.action.token",
      "amount": "$.action.amount"
    },
    "outputs": [{ "kind": "context", "field": "approveUsd", "type": "Decimal", "from": "$.result.usd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "approveUsd": "decimal" } }
}
```
{% endcode %}

***

**TOKEN-004: 승인 금액이 $10,000을 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/12\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
