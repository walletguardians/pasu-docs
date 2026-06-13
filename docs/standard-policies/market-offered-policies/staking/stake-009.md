# STAKE-009: CRV를 본인이 아닌 주소의 veCRV 락에 추가할 시 차단

### Policy Definition (정책 정의)

> CRV를 락에 추가하면서 그 락의 주인이 **본인이 아닌 다른 주소**인 경우, 거래가 나가지 않도록 **차단**합니다.

Curve에서 veCRV 락은 CRV를 묶어두고 그 대가로 투표권과 보상 가중치를 받는 자리입니다. 락에 CRV를 더 넣는 함수는 주인을 따로 지정할 수 있어, 이 주소가 내 지갑이 아니면 내가 넣은 CRV가 남의 락으로 들어가고 그만큼 늘어난 투표권도 그 주소가 가져갑니다. dApp이 미리 채워준 주소를 그대로 서명하거나 받는 주소 칸이 바꿔치기되면, 내 CRV로 엉뚱한 주소의 지분과 의결권만 키워주게 됩니다. 락에 CRV를 더하는 대상이 서명한 본인 지갑과 다르면 경고에 그치지 않고 거래 자체를 막습니다.

#### Scope (적용 범위)

Curve veCRV 락에 CRV를 추가하는 거래에 적용됩니다. 락의 주인으로 지정된 주소가 서명한 본인 지갑과 다른 경우 발동합니다. (Increase Lock Amount)

#### Audience (대상 사용자)

Curve에서 CRV를 락하고 veCRV 투표권을 운용하는 사용자

#### Used Data (판정에 사용될 데이터)

거래가 향하는 베뉴(`context.venue.name` — Curve voting-escrow 여부)와 CRV가 들어갈 락의 주인 주소(`context.onBehalfOf`), 그리고 서명한 본인 지갑 주소(`principal.address`)

#### Policy in Code

{% code title="policy.cedar" %}
```solidity
@id("curve-deposit-for-other-deny")
@severity("deny")
@reason("deposit_for 가 내 CRV를 다른 주소의 veCRV 락에 더합니다 — 락과 투표권이 본인이 아닌 그 주소로 갑니다. 차단했습니다")
forbid (
    principal,
    action == Staking::Action::"IncreaseLockAmount",
    resource
)
when
{
    context.venue.name == "curve_voting_escrow" &&
    context has onBehalfOf &&
    context.onBehalfOf != principal.address
};
```
{% endcode %}

{% code title="manifest.json" %}
```json
{
  "id": "curve-deposit-for-other-deny",
  "schema_version": 2,
  "trigger": {
    "where": {
      "action.domain": {
        "eq": "staking"
      },
      "action.tag": {
        "eq": "increase_lock_amount"
      }
    }
  }
}
```
{% endcode %}

***

**STAKE-009: CRV를 본인이 아닌 주소의 veCRV 락에 추가할 시 차단**\
Wallet Guardians | v.1.0.0 | 26/06/13\
\
\&#xNAN;_Supported Chain: Ethereum_
