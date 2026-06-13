---
description: Unlimited stETH/wstETH Permit Signature to an Untrusted Spender (DENY)
---

# LIDO-015: 신뢰 목록 밖 승인 대상에게 stETH·wstETH 무제한 permit을 서명할 시 차단

### Policy Definition (정책 정의)

> 신뢰 목록에 없는 **승인 대상(Spender)**에게 stETH·wstETH **무제한 permit**을 서명하려는 경우 **차단**합니다.

permit은 트랜잭션 없이 서명 한 번으로 토큰 사용 권한을 넘기는 방식입니다. 한도를 최대치로 적어 서명하면, 그 권한을 받은 승인 대상은 서명이 살아 있는 동안 내 stETH·wstETH 보유량 전부를 언제든 가져갈 수 있습니다. 피싱 사이트가 받아 챙긴 무제한 permit 서명 하나로 스테이킹 물량 전체를 털리는 길이 열립니다. 그래서 Permit2·WithdrawalQueue·wstETH처럼 정상적으로 permit을 받을 곳이 아닌 승인 대상에게 무제한 permit을 서명하려 하면, 서명을 차단합니다.

#### Scope (적용 범위)

대상 토큰이 stETH 또는 wstETH이고, permit 한도가 무제한(uint256 최대치)이며, 받는 승인 대상이 신뢰 목록(Permit2·Lido WithdrawalQueue·wstETH) 밖일 때 적용됩니다. (Erc20Permit)

#### Audience (대상 사용자)

Lido stETH·wstETH를 보유한 스테이커

#### Used Data (판정에 사용될 데이터)

permit을 서명하는 토큰의 주소(`context.token.key.address`), 서명에 담긴 허용 한도(`context.amount`), permit을 받는 승인 대상의 주소(`context.spender`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("steth-wsteth-unlimited-permit-deny")
@severity("deny")
@reason("신뢰 목록 밖의 승인 대상에게 stETH/wstETH 무제한 permit을 서명합니다 — 서명된 permit이 나중에 잔액을 드레인할 수 있어 차단했습니다")
forbid (
    principal,
    action == Token::Action::"Erc20Permit",
    resource
)
when
{
    context.token.key has address &&
    ["0xae7ab96520de3a18e5e111b5eaab095312d7fe84",
     "0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0"].contains
        (
            context.token.key.address
        ) &&
    context.amount == "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff" &&
    !(["0x000000000022d473030f116ddee9f6b43ac78ba3",
      "0x889edc2edab5f40e902b864ad4d7ade8e412f9b1",
      "0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0"].contains
         (
             context.spender
         ))
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "steth-wsteth-unlimited-permit-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "token" },
      "action.tag": { "eq": "erc20_permit" }
    }
  },
  "_template": {
    "lidoTokens": { "cedar_set": "[\"0xae7ab96520de3a18e5e111b5eaab095312d7fe84\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "stETH, wstETH (mainnet)", "meaning": "token addresses this shield applies to (protocol constants)" },
    "approvalAllowlist": { "cedar_set": "[\"0x000000000022d473030f116ddee9f6b43ac78ba3\", \"0x889edc2edab5f40e902b864ad4d7ade8e412f9b1\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "Permit2, WithdrawalQueue, wstETH", "meaning": "spenders for whom an stETH/wstETH permit is expected (extend with trusted DEX/aggregator)" },
    "unlimitedAmount": { "cedar_literal": "\"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff\"", "default": "uint256 MAX", "meaning": "the raw-hex permit value treated as unlimited" }
  }
}
```
{% endcode %}

***

**LIDO-015: 신뢰 목록 밖 승인 대상에게 stETH·wstETH 무제한 permit을 서명할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
