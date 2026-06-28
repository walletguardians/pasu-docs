---
description: 행동 모델(ActionBody)과 Cedar 평가의 기술 레퍼런스
---

# ActionBody & Cedar

이 페이지는 DAMBI의 정책 입력 모델(`ActionBody`)과 Cedar 평가 계약을 기술적으로 설명합니다. 개념 소개는 [정책이란?](../authoring/what-is-a-policy.md)을 먼저 보세요.

## Action = meta + body

정책의 입력은 계층형 **`ActionBody`** 트리입니다. `Action`은 `{ meta, body }` 구조입니다.

* `meta` | 제출 정보. `OnchainTx`(온체인 트랜잭션) vs `OffchainSig`(오프체인 서명)
* `body` | 도메인 변형 (아래 enum)

```rust
// crates/policy-server/asset-model/action/src/lib.rs
pub enum ActionBody {
    Token(TokenAction),
    Amm(AmmAction),
    Lending(LendingAction),
    Airdrop(AirdropAction),
    Launchpad(LaunchpadAction),
    Perp(PerpAction),
    LiquidStaking(LiquidStakingAction),
    Permission(PermissionAction),
    Yield(YieldAction),
    Restaking(RestakingAction),
    Staking(StakingAction),
    Governance(GovernanceAction),
    HyperliquidCore(HyperliquidCoreAction),
    Marketplace(MarketplaceAction),
    Unknown { /* target, chain, calldata, value */ },  // fail-closed
}
```

각 도메인이 다루는 실제 행동은 [액션 종류](../authoring/action-types.md)를 보세요.

* **스키마** | 각 도메인은 `schema/policy-schema/actions/<domain>/`에 Cedar 스키마 조각을, 공통 타입은 `schema/policy-schema/core.cedarschema`에 둡니다.
* **컨텍스트 키** | Cedar 필드 이름 상수는 `crates/policy-engine/src/context_keys.rs`에 정의됩니다.

## 정책 번들 (v2)

정책 하나는 **매니페스트 + Cedar** 의 1:1 짝입니다.

```json
{
  "id": "swap-usdc-amount-cap-deny",
  "schema_version": 2,
  "trigger": {
    "where": { "action.tag": { "eq": "swap" } }
  },
  "policy_rpc": [
    {
      "id": "in-nano",
      "method": "token.normalize_to_nano",
      "params": { "amount": "$.action.direction.amountIn", "decimals": 6 },
      "outputs": [
        { "kind": "context", "field": "amountInNano", "type": "Long",
          "from": "$.result.nano", "required": true }
      ],
      "optional": false
    }
  ],
  "custom_context": { "fields": { "amountInNano": "Long" } }
}
```

| 필드                      | 의미                                                                                |
| ----------------------- | --------------------------------------------------------------------------------- |
| `trigger.where`         | 얕은 필드 선택자 (`action.domain/tag/venue`, `tx.chain_id/from/to`) + 제약(`eq/ne/in/nin`) |
| `policy_rpc[]`          | enrichment 호출 명세 (method, params 선택자, outputs 투영)                                 |
| `custom_context.fields` | Cedar 스키마에 추가될 커스텀 필드 타입 선언                                                       |

## 평가 파이프라인

1. **트리거 평가** (호스트) | 매칭 안 되면 정책 건너뜀. RPC 호출 전에 거름.
2. **plan** | 매칭 시 `policy_rpc` 선택자(`$.action.direction.amountIn` 등)를 구체값으로 해석.
3. **실행** | 해석된 `call_specs`를 policy-server `/evaluate`로 보내 결과 수신.
4. **materialize** | 결과를 `context.custom.*`에 주입.
5. **Cedar 평가** | 정적 필드 + 커스텀 필드로 `forbid` 규칙 검사.

## policy\_rpc 선택자 & enrichment

* **선택자 루트** | `$.root`(tx 필드), `$.action`(lowered 컨텍스트), `$.context`/`$.result`/`$.params`(이전 호출 결과)
* **선택자 해석은 확장 내부에서** 이뤄집니다. raw calldata가 아니라 해석된 파라미터만 서버로 갑니다. → [데이터 흐름](../security/data-flows.md)
* **대표 method** | `oracle.usd_value`(USD 가치), `token.normalize_to_nano`(단위 정규화), `address.sanctions`(제재 검사), `venue.reputation` 등
* **optional/required** | optional 호출 실패는 흡수(fail-open), required 실패는 평가 중단 → fail-closed

## 다음 단계

* 정책 작성 → [에디터로 정책 만들기](../authoring/editor.md)
