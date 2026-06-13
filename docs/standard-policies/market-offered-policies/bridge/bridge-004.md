---
description: Bridge to a Non-Allowlisted Destination Chain (DENY)
---

# BRIDGE-004: 허용되지 않은 체인으로 브리지할 시 차단

### Policy Definition (정책 정의)

> **도착 체인**이 미리 정해 둔 허용 목록에 없으면, 브리지가 나가지 않도록 **차단**합니다.

브리지는 출발 체인에서 자산을 잠그고 도착 체인의 받는 주소로 같은 자산을 다시 풀어주는 식으로 동작합니다. 허용되지 않은 체인을 고르면 전송 자체를 차단합니다.&#x20;

#### Scope (적용 범위)

도착 체인이 허용 목록에 없을 때 적용됩니다.

#### Audience (대상 사용자)

자산을 보유하는 체인이 정해져 있어, 그 밖으로는 절대 보내지 않으려는 사용자

#### Used Data (판정에 사용될 데이터)

도착 체인 식별자(`context.dstChainId`)가 허용 목록(`allowedDstChains`)의 포함되어 있는지 확인합니다.

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-dst-chain-not-allowlisted-deny")
@severity("deny")
@reason("허용한 도착 체인 목록에 없는 체인으로 보내려 합니다 — 도착 체인을 확인하세요.")
forbid (
    principal,
    action == Bridge::Action::"Send",
    resource
)
when
{
    !(["eip155:1",
      "eip155:10",
      "eip155:56",
      "eip155:130",
      "eip155:137",
      "eip155:8453",
      "eip155:42161",
      "eip155:59144",
      "eip155:999",
      "eip155:43114"].contains
         (
             context.dstChainId
         ))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "bridge-dst-chain-not-allowlisted-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "bridge" },
      "action.tag": { "eq": "send" }
    }
  },
  "_template": {
    "allowedDstChains": {
      "cedar_set": "[\"eip155:1\", \"eip155:10\", \"eip155:56\", \"eip155:130\", \"eip155:137\", \"eip155:8453\", \"eip155:42161\", \"eip155:59144\", \"eip155:999\", \"eip155:43114\"]",
      "default": "Ethereum, Optimism, BNB, Unichain, Polygon, Base, Arbitrum, Linea, HyperEVM, Avalanche — the usage-calibrated set covering ~97-99% of real Across + Li.Fi EVM destinations (Dune q7686050 / q7686070)",
      "meaning": "CAIP-2 destination chains the user expects to bridge to; bridging to any chain NOT in this set denys. NOTE: Li.Fi non-EVM destinations (Solana, ~23% of Li.Fi traffic) arrive as Raw{bytes32} and DENY by default here — intended (also caught by bridge-recipient-raw-nonevm-deny). Across also supports zkSync(324)/Scroll(534352)/Blast(81457) — add them if you use those routes. Narrow to lock down (e.g. only Base + Arbitrum)."
    }
  }
}
```
{% endcode %}

***

**BRIDGE-004: 허용되지 않은 도착 체인으로 브리지할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
