# 신뢰 목록 외의 주소에서 stETH·wstETH permit 서명을 요청할 시 경고

### Policy Definition (정책 정의)

> 신뢰 목록 밖의 \*\*승인 대상(Spender)\*\*에게 stETH·wstETH permit을 서명하려는 경우 경고합니다.

permit은 트랜잭션 없이 서명 한 번으로 토큰 사용 권한을 위임하는 방식입니다. stETH나 wstETH permit에 서명하면 승인 대상은 서명에 담긴 한도만큼 내 stETH·wstETH를 직접 옮길 수 있습니다. Lido에서 자주 쓰는 승인 대상은 정해져 있습니다.

(1) wstETH로 감쌀 때 쓰는 wstETH 컨트랙트

(2) 출금을 신청하는 Withdrawal Queue

(3) 한 번의 서명으로 권한을 위임하는 Permit2

이 정책은 위 셋에 해당하지 않는 주소가 stETH·wstETH permit 서명을 요청하는 경우 승인 대상을 확인하도록 경고합니다.

#### Scope (적용 범위)

stETH 또는 wstETH permit 서명에 적용됩니다.

#### Audience (대상 사용자)

stETH·wstETH를 보유하고 Lido로 스테이킹·출금을 다루는 사용자

#### Used Data (판정에 사용될 데이터)

* permit을 서명하는 토큰 주소(`context.token.key.address`)
* 서명에 담긴 한도(`context.amount`)
* 권한을 받아 갈 승인 대상(`context.spender`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("steth-wsteth-permit-non-allowlisted-warn")
@severity("warn")
@reason("신뢰 목록 외의 주소에서 stETH/wstETH permit을 서명을 요청합니다")
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
    context.amount != "0x0" &&
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
  "id": "steth-wsteth-permit-non-allowlisted-warn",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "token" },
      "action.tag": { "eq": "erc20_permit" }
    }
  },
  "_template": {
    "lidoTokens": { "cedar_set": "[\"0xae7ab96520de3a18e5e111b5eaab095312d7fe84\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "stETH, wstETH (mainnet)", "meaning": "token addresses this shield applies to (protocol constants)" },
    "approvalAllowlist": { "cedar_set": "[\"0x000000000022d473030f116ddee9f6b43ac78ba3\", \"0x889edc2edab5f40e902b864ad4d7ade8e412f9b1\", \"0x7f39c581f595b53c5cb19bd0b3f8da6c935e2ca0\"]", "default": "Permit2, WithdrawalQueue, wstETH", "meaning": "spenders for whom an stETH/wstETH permit is expected" },
    "zeroAmount": { "cedar_literal": "\"0x0\"", "default": "uint256 zero", "meaning": "zero-value permits clear/reset allowance and should not warn" },
  }
}
```
{% endcode %}

***

**LIDO-014: 신뢰 목록에 없는 대상에게 stETH·wstETH permit을 서명할 시 경고**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
