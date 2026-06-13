---
description: Bridge Output Value Loss Above Cap (WARN)
---

# BRIDGE-007: 브리지 손실이 한도를 초과할 시 경고

### Policy Definition (정책 정의)

> 브리지로 보낸 가치 대비 **도착 체인에서 받을 가치**가 설정 한도 넘게 줄어드는 경우, 금액을 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 따라서 보낸 금액과 받는 금액이 거의 같아야 정상입니다. 수수료나 토큰을 바꿔 건너는 경로라면 1\~3% 정도는 차이가 날 수 있지만, 그보다 훨씬 크게 벌어진다면 화면에 적힌 받을 금액이 비정상적으로 낮게 잡혔다는 신호입니다. 보낸 가치와 받을 가치를 USD로 환산해 비교했을 때 차이가 기준을 넘으면 서명 전에 한 번 더 확인하도록 경고합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 요청에 적용됩니다. 보내는 자산과 도착 체인에서 받을 자산을 USD로 환산해 줄어든 비율이 한도를 넘을 때(기본 5%) 적용됩니다.

#### Audience (대상 사용자)

여러 체인에 자산을 나눠 두고 브리지로 자주 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

보내는 자산과 도착 체인에서 받을 자산을 USD로 환산해 줄어든 비율(`context.custom.bridgeValueLossPct`)이 한도 이하인지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-output-value-loss-warn")
@severity("warn")
@reason("보내는 자산에 비해 도착 체인에서 받는 자산이 크게 적습니다 — 손실이 설정 한도를 넘으니 도착 체인에서 받을 토큰과 금액을 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    context has custom &&
    context.custom has bridgeValueLossPct &&
    context.custom.bridgeValueLossPct.greaterThan(decimal("5.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-output-value-loss-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "policy_rpc": [
    {
      "id": "bridge-value-loss",
      "method": "bridge.value_loss_pct",
      "params": {
        "src_chain_id": "$.root.chain_id",
        "src_asset": "$.action.srcToken.key.address",
        "input_amount": "$.action.inputAmount",
        "dst_chain_id": "$.action.dstChainId",
        "dst_asset": "$.action.dstToken.key.address",
        "output_amount": "$.action.outputAmount"
      },
      "outputs": [
        {
          "kind": "context",
          "field": "bridgeValueLossPct",
          "type": "Decimal",
          "from": "$.result.loss_pct",
          "required": false
        }
      ],
      "optional": true
    }
  ],
  "custom_context": {
    "fields": {
      "bridgeValueLossPct": "decimal"
    }
  },
  "_template": {
    "maxValueLossPct": {
      "cedar_decimal": "5.0000",
      "default": "5.0000 (%)",
      "meaning": "warn when a bridge's implied value loss ((inputUsd - outputUsd) / inputUsd * 100, computed by bridge.value_loss_pct) exceeds this %. Normal bridge fees ~0.1-1% (cross-token swap ~1-3%) pass; an abnormal skim (a frontend that sets outputAmount absurdly low) is flagged. CONSERVATIVE default — NOT yet Dune-calibrated (TODO: measure the same-stablecoin Across (inputAmount-outputAmount)/inputAmount p99). Dynamic: the method bridge.value_loss_pct IS served server-side (handler.rs); requires login + BOTH token prices synced + dstToken/outputAmount statically known, otherwise dormant (fail-open)."
    },
    "_note_selectors": "params `$.action.*` resolve against the LOWERED Cedar context (camelCase): srcToken.key.address / inputAmount / dstChainId / dstToken.key.address / outputAmount — NOT the snake_case ActionBody. dstToken/outputAmount are OPTIONAL (absent on some routes that do not statically expose the output) → selector miss → call skipped → dormant."
  }
}
```
{% endcode %}

***

**BRIDGE-007: 브리지 수취 가치 손실이 한도를 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
