# stETH가 ETH보다 쌀 때 Lido에 스테이킹할 시 경고

### Policy Definition (정책 정의)

> Lido에 ETH를 스테이킹하려는 순간 **stETH가 ETH보다 눈에 띄게 싸게 거래되고 있으면** 경고합니다.

Lido에 ETH를 스테이킹하면 같은 가치의 stETH를 1:1로 받습니다. 그런데 stETH는 시장에서 따로 거래되기 때문에 가격이 ETH와 잠깐씩 벌어질 때가 있습니다. stETH가 ETH보다 싸게 거래되는 동안에는 Lido에 직접 스테이킹 하기보다 시장에서 stETH를 사는 편이 이득입니다.

이 정책은 stETH 시세가 ETH보다 1.00%(100bp) 넘게 낮은 경우 스테이킹하기 전에 시장 매수를 한 번 따져보라고 알립니다.

#### Scope (적용 범위)

Lido에 ETH를 맡겨 stETH를 받는 스테이킹에 적용됩니다. (Liquid Staking / Stake)

#### Audience (대상 사용자)

Lido에 ETH를 새로 스테이킹하려는 사용자

#### Used Data (판정에 사용될 데이터)

* 스테이킹하려는 프로토콜(`context.venue.name`)
* 서버에서 조회한 stETH의 현재 시세 괴리율(`context.custom.stethDiscountBps`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("stake-during-steth-discount-warn")
@severity("warn")
@reason("지금 stETH가 ETH보다 쌉니다. 지금은 스테이킹보다 시장에서 stETH를 사는 편이 유리할 수 있습니다")
forbid (
    principal,
    action == LiquidStaking::Action::"Stake",
    resource
)
when
{
    context.venue.name == "lido" &&
    context has custom &&
    context.custom has stethDiscountBps &&
    context.custom.stethDiscountBps.greaterThan(decimal("100.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "stake-during-steth-discount-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "stake" }
    }
  },
  "policy_rpc": [
    {
      "id": "peg",
      "method": "oracle.steth_peg_status_bps",
      "params": { "chain_id": "$.root.chain_id" },
      "outputs": [
        { "kind": "context", "field": "stethDiscountBps", "type": "Decimal", "from": "$.result.discountBps", "required": false }
      ],
      "optional": true
    }
  ],
  "custom_context": { "fields": { "stethDiscountBps": "decimal" } },
  "_template": {
    "maxStethDiscountBps": { "cedar_literal": "decimal(\"100.0000\")", "default": "100 bps (1.00%)", "meaning": "warn when stETH trades below ETH by more than this discount" }
  }
}
```
{% endcode %}

***

**LIDO-002: stETH가 ETH보다 싸게 거래될 때 스테이킹할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
