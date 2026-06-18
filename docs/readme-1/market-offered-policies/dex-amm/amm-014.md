---
description: Adding Liquidity to a Pool with Very Low TVL (WARN)
---

# AMM-014: 잠겨있는 자산(TVL)이 낮은 풀에 유동성을 공급 시 경고

### Policy Definition (정책 정의)

> **TVL이 기준보다 낮은** 경우, 공급 전에 다시 확인하도록 경고합니다.

풀에 잠긴 자산(TVL)은 풀 규모를 가늠하는 1차 지표입니다. TVL이 매우 작으면 출금 시 받아줄 상대 유동성이 부족하고, 적은 금액으로도 가격이 크게 흔들릴 가능성이 큽니다. 풀의 TVL이 기준에 못 미치면, 정말 이 풀에 유동성을 공급할지 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

풀에 잠긴 총 자산(TVL)이 기준에 못 미치는 경우에 적용됩니다.

#### Audience (대상 사용자)

신생 풀이나 잘 알려지지 않은 풀에 LP로 들어가는 사용자

#### Used Data (판정에 사용될 데이터)

해당 풀에 잠긴 총 자산(`buyTokenFdvUsd`)이 기준보다 낮은지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("addliquidity-pool-tvl-floor-warn")
@severity("warn")
@reason("이 풀에 잠긴 유동성(TVL)이 매우 적습니다 — 대상 풀을 확인하세요")
forbid(principal, action == Amm::Action::"AddLiquidity", resource)
when {
  context has custom
  && context.custom has poolTvlUsd
  && context.custom.poolTvlUsd.lessThan(decimal("50000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "addliquidity-pool-tvl-floor-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "add_liquidity" } } },
  "policy_rpc": [{
    "id": "pool-tvl",
    "method": "pool.liquidity",
    "params": { "chain_id": "$.root.chain_id", "venue": "$.action.venue" },
    "outputs": [{ "kind": "context", "field": "poolTvlUsd", "type": "Decimal", "from": "$.result.reserveUsd", "required": false }],
    "optional": true
  }],
  "custom_context": { "fields": { "poolTvlUsd": "decimal" } }
}
```
{% endcode %}

***

**AMM-014:** 잠겨있는 자산(TVL)이 낮은 풀에 유동성을 공급 시 경고\
Wallet Guardians | v.1.0.0 | 26/06/16\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
