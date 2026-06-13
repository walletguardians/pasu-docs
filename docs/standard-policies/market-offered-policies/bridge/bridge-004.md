---
description: Bridge to a Non-Allowlisted Destination Chain (DENY)
---

# BRIDGE-004: 허용되지 않은 도착 체인으로 브리지할 시 차단

### Policy Definition (정책 정의)

> **도착 체인**이 미리 정해 둔 허용 목록에 없으면, 브리지가 나가지 않도록 **차단**합니다.

브리지는 출발 체인에서 자산을 잠그고, 지정한 도착 체인에서 받는 주소로 같은 가치를 풀어 줍니다. 그래서 어느 체인으로 보내는지(`context.dstChainId`)가 자산이 도착할 곳을 결정합니다. 잘못된 체인을 고르면 그쪽에서 내 주소를 쓸 수 없거나 회수 경로가 막혀 자산이 그대로 묶입니다. 출발 체인에서 이미 자산이 빠져나간 뒤라 되돌릴 수도 없습니다. 따라서 도착 체인이 허용 목록에 없으면 경고에 그치지 않고 브리지 자체를 막습니다.

#### Scope (적용 범위)

Across·Li.Fi 등으로 보내는 브리지(Send)에 적용됩니다. 도착 체인(`context.dstChainId`)이 허용 목록에 없을 때 발동하며, 기본 목록은 Across·Li.Fi의 실제 EVM 트래픽 대부분을 차지하는 10개 체인(Ethereum, Optimism, BNB, Unichain, Polygon, Base, Arbitrum, Linea, HyperEVM, Avalanche)을 CAIP-2 형식으로 담고 있습니다.

#### Audience (대상 사용자)

브리지하는 체인이 정해져 있어, 그 밖으로는 절대 보내지 않으려는 사용자

#### Used Data (판정에 사용될 데이터)

도착 체인 식별자 (`context.dstChainId` — CAIP-2 형식, 예: `eip155:1`)와 허용 목록(`allowedDstChains`)의 포함 여부

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("bridge-dst-chain-not-allowlisted-deny")
@severity("deny")
@reason("허용한 도착 체인 목록에 없는 체인으로 보내려 합니다 — 정책(허용 목록 잠금)에 따라 차단했습니다")
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
      "meaning": "CAIP-2 destination chains the user expects to bridge to; bridging to any chain NOT in this set warns. NOTE: Li.Fi non-EVM destinations (Solana, ~23% of Li.Fi traffic) arrive as Raw{bytes32} and WARN by default here — intended (also caught by bridge-recipient-raw-nonevm-warn). Across also supports zkSync(324)/Scroll(534352)/Blast(81457) — add them if you use those routes. Narrow to lock down (e.g. only Base + Arbitrum)."
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
