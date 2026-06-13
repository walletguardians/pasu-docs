---
description: Large Single Stake of ETH to Lido (DENY)
---

# LIDO-001: Lido에 100 ETH 이상을 한 번에 스테이킹할 시 차단

### Policy Definition (정책 정의)

> 한 번에 **약 100 ETH 이상**을 Lido에 스테이킹하는 대량 거래를 **차단**합니다.

Lido 스테이킹은 ETH를 맡기고 그만큼 stETH를 받는 거래입니다. 금액이 클수록 입력 실수나 피싱 사이트가 유도한 거래일 때 한 번에 잃는 액수가 커집니다. 그래서 한 거래에서 맡기는 ETH가 약 100개 이상이면 차단합니다.

#### Scope (적용 범위)

Lido에 ETH를 맡기는 스테이킹 거래 중, 한 번에 맡기는 양이 약 100 ETH 이상일 때 적용됩니다. (Stake)

#### Audience (대상 사용자)

Lido로 ETH를 스테이킹하는 사용자, 특히 한 번에 큰 금액을 맡기는 거래를 사전에 막아두려는 사용자

#### Used Data (판정에 사용될 데이터)

거래가 향하는 venue가 Lido인지(`context.venue.name`), 그리고 이번에 맡기는 ETH 수량(`context.amountNano`, 1 ETH = 10억 단위). 수량이 100,000,000,000(=약 100 ETH) 이상이면 차단합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("stake-amount-guard-deny")
@severity("deny")
@reason("약 100 ETH 이상을 Lido에 한 번에 스테이킹하는 대량 거래입니다 — 차단했습니다")
forbid (
    principal,
    action == LiquidStaking::Action::"Stake",
    resource
)
when
{
    context.venue.name == "lido" &&
    context has amountNano &&
    context.amountNano >= 100000000000
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "stake-amount-guard-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "stake" }
    }
  },
  "_template": {
    "maxStakeNano": { "cedar_literal": "100000000000", "default": "100 ETH (1 ETH = 1e9 amountNano)", "meaning": "deny when a single stake's amountNano >= this" }
  }
}
```
{% endcode %}

***

**LIDO-001: Lido에 100 ETH 이상을 한 번에 스테이킹할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
