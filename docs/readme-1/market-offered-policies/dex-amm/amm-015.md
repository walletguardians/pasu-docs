---
description: Swapping Into a Very Small-Cap Token (WARN)
---

# AMM-015: 시가총액(FDV)이 작은 토큰을 매수할 시 경고

### Policy Definition (정책 정의)

> 스왑으로 **사려는 토큰의 시가총액이 매우 작은** 경우, 진행하기 전에 다시 확인하도록 경고합니다.

시가총액이 아주 작은 토큰은 소수의 물량만으로도 가격이 크게 흔들릴 가능성이 높고, 러그풀이 일어나기 쉽습니다. 받게 될 토큰의 시가총액이 기준에 못 미치면, 정말 이 토큰으로 바꿀 것인지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

받게 될 토큰의 시가총액(FDV)이 기준에 못 미치는 경우에 적용됩니다.

#### Audience (대상 사용자)

유동성이 얕은 신규, 소형 토큰을 Swap으로 매수하는 사용자

#### Used Data (판정에 사용될 데이터)

받게 될 토큰의 시가총액(`buyTokenLiquidityUsd`)이 기준보다 낮은지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("swap-buy-microcap-warn")
@severity("warn")
@reason("매수하려는 토큰의 시가총액(FDV)이 기준보다 작습니다 — 대상 토큰을 확인하세요")
forbid(principal, action == Amm::Action::"Swap", resource)
when {
  context has custom
  && context.custom has buyTokenFdvUsd
  && context.custom.buyTokenFdvUsd.lessThan(decimal("1000000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "swap-buy-microcap-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "swap" } } },
  "policy_rpc": [{
    "id": "buy-mkt",
    "method": "token.market_data",
    "params": { "chain_id": "$.root.chain_id", "token": "$.action.tokenOut.key.address" },
    "outputs": [{ "kind": "context", "field": "buyTokenFdvUsd", "type": "Decimal", "from": "$.result.fdvUsd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "buyTokenFdvUsd": "decimal" } }
}
```
{% endcode %}

***

**AMM-015: 시가총액(FDV)이 매우 작은 토큰을 매수할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
