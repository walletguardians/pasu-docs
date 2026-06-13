---
description: Bridge Input Above USD Cap (WARN)
---

# BRIDGE-006: 브리지 금액이 USD 한도를 초과할 시 경고

### Policy Definition (정책 정의)

> 한 번의 **브리지로 옮기는 금액**이 설정한 USD 한도를 넘는 경우, 이만큼 보낼지 다시 확인하도록 경고합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인에서 받는 주소로 같은 값을 풀어주는 식으로 자산을 옮깁니다. 한 번에 큰 금액을 보낼수록 받는 주소나 도착 체인을 잘못 넣었을 때 잃는 돈도 그만큼 커지고, 브리지는 한 번 출발하면 중간에 멈추거나 되돌리기 어렵습니다. 그래서 보내는 금액을 시세로 환산한 값이 한도를 넘으면, 정말 이 규모를 옮길지 서명 전에 한 번 더 짚어보도록 경고합니다.

#### Scope (적용 범위)

브리지로 자산을 보내는 서명에 적용됩니다. 보내는 토큰과 수량을 USD로 환산한 값이 한도(기본 $100,000)를 넘을 때 발동합니다. (Bridge Send)

#### Audience (대상 사용자)

여러 체인을 오가며 큰 금액을 브리지로 옮기는 사용자

#### Used Data (판정에 사용될 데이터)

보내는 토큰과 수량을 USD로 환산한 값 (`context.custom.bridgeInputUsd` — 서버 시세 조회 결과). 출발 토큰(`$.action.srcToken.key.address`)과 보내는 수량(`$.action.inputAmount`)을 시세에 곱해 산출합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-input-usd-cap-warn")
@severity("warn")
@reason("한 번의 브리지로 설정한 USD 한도를 넘는 금액을 옮깁니다 — 이만큼 보낼지 확인하세요")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    context has custom &&
    context.custom has bridgeInputUsd &&
    context.custom.bridgeInputUsd.greaterThan(decimal("100000.0000"))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-input-usd-cap-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "policy_rpc": [
    {
      "id": "bridge-in-usd",
      "method": "oracle.usd_value",
      "params": {
        "chain_id": "$.root.chain_id",
        "asset": "$.action.srcToken.key.address",
        "amount": "$.action.inputAmount"
      },
      "outputs": [
        {
          "kind": "context",
          "field": "bridgeInputUsd",
          "type": "Decimal",
          "from": "$.result.usd",
          "required": false
        }
      ],
      "optional": true
    }
  ],
  "custom_context": {
    "fields": {
      "bridgeInputUsd": "decimal"
    }
  },
  "_template": {
    "maxBridgeUsd": {
      "cedar_decimal": "100000.0000",
      "default": "100000.0000 (USD)",
      "meaning": "warn when a single bridge moves more than this USD value (oracle.usd_value of srcToken × inputAmount). Default $100k flags only ~0.3% of Across / ~0.9% of Li.Fi deposits (Dune q7686050 / q7686070) — the clean whale/outlier tail. Dynamic: requires login + synced prices, otherwise dormant (fail-open)."
    },
    "_note_selectors": "params `$.action.*` resolve against the LOWERED Cedar context (camelCase): srcToken.key.address + inputAmount, NOT the snake_case ActionBody."
  }
}
```
{% endcode %}

***

**BRIDGE-006: 브리지 금액이 USD 한도를 초과할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
