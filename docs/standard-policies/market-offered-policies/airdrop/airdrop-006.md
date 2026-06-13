---
description: Airdrop Claim via Off-Chain Signature Distributor (WARN)
---

# AIRDROP-006: 에어드롭을 오프체인 서명으로 클레임할 시 경고

### Policy Definition (정책 정의)

> 에어드롭을 **오프체인 서명**으로 클레임하는 경우, 단순 클레임을 넘는 권한이 딸려 있을 수 있어 경고를 표시합니다.

에어드롭 클레임에는 두 가지 방식이 있습니다. 하나는 컨트랙트를 직접 호출해 토큰을 받는 온체인 클레임이고, 다른 하나는 가스비를 아낀다며 서명만 받아 처리하는 오프체인 서명 방식입니다. 문제는 오프체인 서명에 무엇이 담겼는지 지갑 화면만 봐서는 알기 어렵다는 점입니다. 겉으로는 토큰을 받는 서명처럼 보여도, 실제로는 토큰 사용 권한을 내주거나 다른 주소로 자산을 옮기는 내용이 함께 들어 있을 수 있습니다. 그래서 클레임이 오프체인 서명 방식으로 잡히면, 서명에 어떤 권한이 묶여 있는지 미리 확인하도록 경고합니다.

#### Scope (적용 범위)

에어드롭 클레임에 적용됩니다. 클레임이 오프체인 서명 방식(`signature_distributor`)으로 진행될 때 발동합니다. (Claim)

#### Audience (대상 사용자)

에어드롭을 받으려고 서명 클레임 사이트를 자주 들르는 사용자

#### Used Data (판정에 사용될 데이터)

이번 클레임의 진행 방식 (`context.claimTarget.kind` — 오프체인 서명 클레임인지 여부)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("claim-signature-distributor-confirm-warn")
@severity("warn")
@reason("이 에어드롭은 오프체인 서명으로 클레임합니다 — 단순 클레임 이상의 권한을 줄 수 있으니 서명 전에 확인하세요")
forbid(principal, action == Airdrop::Action::"Claim", resource)
when {
  context.claimTarget.kind == "signature_distributor"
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "claim-signature-distributor-confirm-warn",
  "schema_version": 2,
  "trigger": { "where": { "action.tag": { "eq": "claim" } } }
}
```
{% endcode %}

***

**AIRDROP-006: 에어드롭을 오프체인 서명으로 클레임할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
