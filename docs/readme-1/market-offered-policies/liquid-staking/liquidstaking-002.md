# LIQUID\_STAKING-002: stETH 지분을 소각 주소로 전송할 시 차단

### Policy Definition (정책 정의)

> stETH 지분(TransferShares)을 **소각 주소**로 보내려는 경우 차단합니다.

stETH는 보유량이 매일 늘어나는 리베이스 토큰이라, 지분(share) 단위로도 옮길 수 있습니다. 이 지분을 소각 주소(`0x000…000` / `0x…dead`)로 보내면 stETH는 그대로 소각됩니다.

이 정책은 받는 주소가 소각 주소인 경우 차단합니다.

#### Scope (적용 범위)

Lido stETH의 지분 전송에 적용됩니다.

#### Audience (대상 사용자)

stETH를 보유하고 지분 단위로 전송하는 Lido 사용자

#### Used Data (판정에 사용될 데이터)

* 지분을 받을 주소(`context.recipient`)
* 거래가 일어나는 venue(`context.venue.name`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("transfer-shares-burn-recipient-deny")
@severity("deny")
@reason("지분 전송 수신자가 소각 주소입니다")
forbid (
    principal,
    action == LiquidStaking::Action::"TransferShares",
    resource
)
when
{
    context.venue.name == "lido" &&
    ["0x0000000000000000000000000000000000000000",
     "0x000000000000000000000000000000000000dead"].contains
        (
            context.recipient
        )
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "transfer-shares-burn-recipient-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": { "eq": "liquid_staking" },
      "action.tag": { "eq": "transfer_shares" }
    }
  },
  "_template": {
    "burnAddresses": { "cedar_set": "[\"0x0000000000000000000000000000000000000000\", \"0x000000000000000000000000000000000000dead\"]", "default": "zero + dead", "meaning": "addresses whose funds are permanently unrecoverable (protocol constants)" }
  }
}
```
{% endcode %}

***

LIQUID\_STAKING-002: stETH 지분을 소각 주소로 전송할 시 차단\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
