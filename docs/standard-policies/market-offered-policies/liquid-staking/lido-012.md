---
description: stETH/wstETH Approval to a Non-Allowlisted Spender (WARN)
---

# LIDO-012: 신뢰 목록 밖 주소에 stETH/wstETH 승인 시 경고

### Policy Definition (정책 정의)

> **신뢰 목록에 없는 승인 대상(Spender)**에게 stETH 또는 wstETH 사용을 승인하는 경우 경고합니다.

토큰 승인(Approve)은 승인 대상에게 내 토큰을 한도만큼 대신 옮길 수 있는 권한을 주는 것입니다. Lido에서 자주 쓰는 승인 대상은 정해져 있습니다. wstETH로 감쌀 때 쓰는 wstETH 컨트랙트, 출금을 신청하는 Withdrawal Queue, 그리고 한 번의 서명으로 권한을 위임하는 Permit2 정도입니다. 이 셋이 아닌 처음 보는 주소가 stETH·wstETH 승인을 요청한다면, 가짜 사이트가 내 스테이킹 자산을 빼내려는 신호일 수 있습니다. 그래서 신뢰 목록 밖의 주소에 stETH나 wstETH 승인을 보내려 하면, 한도를 주기 전에 승인 대상을 다시 확인하도록 경고합니다.

#### Scope (적용 범위)

stETH(`0xae7a…fe84`) 또는 wstETH(`0x7f39…2ca0`) 승인에 적용됩니다. 한도를 0으로 되돌리는 취소 승인과 무제한(uint256 최대치) 승인은 제외하고, 승인 대상이 Permit2·Withdrawal Queue·wstETH가 아닐 때 발동합니다. (Erc20Approve)

#### Audience (대상 사용자)

stETH·wstETH를 보유하거나 Lido로 스테이킹하는 사용자

#### Used Data (판정에 사용될 데이터)

ERC-20 Approve일 때, 승인하는 토큰 주소(`context.token.key.address`), 승인 한도(`context.amount`), 승인 대상의 주소(`context.spender`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("steth-wsteth-approve-non-allowlisted-warn")
@severity("warn")
@reason("신뢰 목록 밖의 승인 대상(Spender)에게 stETH/wstETH 사용을 승인합니다 — 한도를 주기 전에 승인 대상을 확인하세요")
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
    context.amount != "0x0" &&
    context.amount != "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff" &&
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
  "id": "steth-wsteth-approve-non-allowlisted-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "token" },
      "action.tag": { "eq": "erc20_approve" }
    }
  },
  "_template": {
    "lidoTokens": { "cedar_set": "[\"0xae7ab96520de3a18e5e111b5eaab095312d7fe84\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "stETH, wstETH (mainnet)", "meaning": "token addresses this shield applies to (protocol constants)" },
    "approvalAllowlist": { "cedar_set": "[\"0x000000000022d473030f116ddee9f6b43ac78ba3\", \"0x889edc2edab5f40e902b864ad4d7ade8e412f9b1\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "Permit2, WithdrawalQueue, wstETH", "meaning": "spenders for whom an stETH/wstETH approval is expected" },
    "zeroAmount": { "cedar_literal": "\"0x0\"", "default": "uint256 zero", "meaning": "zero-value approvals clear/reset allowance and should not warn" },
    "unlimitedAmount": { "cedar_literal": "\"0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff\"", "default": "uint256 MAX", "meaning": "excluded here because the unlimited sibling denies it" }
  }
}
```
{% endcode %}

***

**LIDO-012: 신뢰 목록 밖 주소에 stETH/wstETH 승인 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
