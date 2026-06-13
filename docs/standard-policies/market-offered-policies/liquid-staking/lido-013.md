---
description: Unlimited stETH/wstETH Approval to an Untrusted Spender (DENY)
---

# LIDO-013: stETH·wstETH를 신뢰 목록 밖 상대에게 무제한 승인할 시 차단

### Policy Definition (정책 정의)

> stETH 또는 wstETH를 **신뢰 목록 밖의 승인 대상(Spender)**에게 무제한 한도로 승인하려는 경우 차단합니다.

토큰 승인은 승인 대상(Spender)에게 내 토큰을 대신 옮길 권한을 주는 것과 같고, 한도를 최대치로 잡으면 그 상대는 권한이 살아 있는 동안 내 stETH·wstETH 보유량 전부를 언제든 가져갈 수 있습니다. Lido에서 stETH·wstETH 무제한 승인이 정상적으로 필요한 곳은 Permit2, 출금을 처리하는 Withdrawal Queue, stETH를 감싸는 wstETH 컨트랙트 정도로 정해져 있습니다. 그 목록을 벗어난 주소가 무제한 승인을 요구하는 건 토큰을 통째로 빼가려는 드레인 셋업에서 흔히 보이는 신호입니다. 따라서 신뢰 목록 밖 상대에게 stETH·wstETH를 무제한 승인하려 하면 서명을 차단합니다.

#### Scope (적용 범위)

stETH(`0xae7ab9…fe84`) 또는 wstETH(`0x7f39c5…2ca0`) 승인에 적용됩니다. 한도가 uint256 최대치이고 승인 대상이 신뢰 목록(Permit2, Withdrawal Queue, wstETH)에 없을 때 발동합니다. (Erc20Approve)

#### Audience (대상 사용자)

stETH·wstETH를 보유하거나 Lido로 스테이킹하는 사용자

#### Used Data (판정에 사용될 데이터)

승인 대상 토큰의 주소(`context.token.key.address`), 승인 한도(`context.amount`), 그리고 권한을 받는 승인 대상의 주소(`context.spender`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("steth-wsteth-unlimited-approve-deny")
@severity("deny")
@reason("신뢰 목록 밖의 승인 대상에게 stETH/wstETH를 무제한 승인합니다 — 전형적인 토큰 탈취 셋업이라 차단했습니다")
forbid (
    principal,
    action == Token::Action::"Erc20Approve",
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
  "id": "steth-wsteth-unlimited-approve-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "token" },
      "action.tag": { "eq": "erc20_approve" }
    }
  },
  "_template": {
    "lidoTokens": { "cedar_set": "[\"0xae7ab96520de3a18e5e111b5eaab095312d7fe84\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "stETH, wstETH (mainnet)", "meaning": "token addresses this shield applies to (protocol constants)" },
    "approvalAllowlist": { "cedar_set": "[\"0x000000000022d473030f116ddee9f6b43ac78ba3\", \"0x889edc2edab5f40e902b864ad4d7ade8e412f9b1\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "Permit2, WithdrawalQueue, wstETH", "meaning": "spenders for whom an stETH/wstETH approval is expected (extend with trusted DEX/aggregator)" },
    "unlimitedAmount": { "cedar_literal": "\"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff\"", "default": "uint256 MAX", "meaning": "the raw-hex value treated as unlimited" }
  }
}
```
{% endcode %}

***

**LIDO-013: stETH·wstETH를 신뢰 목록 밖 상대에게 무제한 승인할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
