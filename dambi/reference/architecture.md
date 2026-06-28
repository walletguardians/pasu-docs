---
description: 가로채기 → 해석 → 평가 → 판정으로 이어지는 DAMBI의 동작 구조
---

# 아키텍처 개요

DAMBI는 지갑 RPC를 가로채 의미 있는 행동으로 해석하고, 정책을 평가해 서명 전에 판정을 내립니다. 이 페이지는 그 전체 흐름과 구성 요소를 설명합니다.

## 전체 데이터 흐름

```mermaid
flowchart TD
    A["dApp · window.ethereum"] -->|"eth_sendTransaction<br/>eth_signTypedData<br/>personal_sign"| B["확장프로그램 서비스 워커"]
    B --> C["디코드<br/>(registryV2 어댑터 매니페스트)"]
    C --> D["ActionBody 트리<br/>(의미 단위 행동)"]
    D --> E["Cedar 요청으로 lowering"]
    E --> F["WASM Cedar 엔진<br/>(기기 안 평가)"]
    F --> G{"판정<br/>Pass / Warn / Fail"}
    B -.선택적.-> H["policy-server<br/>인증 · 지갑 상태 · 예측 델타 · remote facts"]
    H -.->|"USD 가치 등<br/>remote facts"| F
```

1. **가로채기** — 확장프로그램이 dApp의 지갑 트랜잭션·EIP-712/personal 서명 요청을 가로챕니다.
2. **해석(디코드)** — `registryV2` 매니페스트 기반 디코더가 raw calldata/typed-data를 타입이 있는 **`ActionBody`** 트리로 변환합니다.
3. **lowering & 평가** — Rust 엔진이 `ActionBody`를 Cedar 요청으로 낮춰(lower) 설치된 정책으로 평가합니다. **모두 서비스 워커 안 WASM에서** 처리되어, 판정에는 네트워크 왕복이 필요 없습니다.
4. **판정** — `Pass / Warn / Fail`을 사용자에게 보여줍니다. ([판정 이해하기](../getting-started/verdicts.md))
5. **백엔드(선택)** — `policy-server`는 Google OAuth, 지갑/계정 상태, 예측 `StateDelta`를 제공합니다. **최종 판정은 확장프로그램이 소유**하고, 서버는 상태 컨텍스트만 보탭니다.

## 구성 요소

| 구성 요소                         | 역할                                                                    |
| ----------------------------- | --------------------------------------------------------------------- |
| **브라우저 확장프로그램**               | 가로채기·디코드·평가·확인 UI. 서비스 워커(평가), 콘텐츠 스크립트, 팝업, 확인 페이지, 옵션 페이지(대시보드)로 구성 |
| **대시보드**                      | 정책 작성·관리, 정책 허브, 시뮬레이션, 히스토리를 담은 React 관리 UI                          |
| **정책 엔진 (Rust/Cedar)**        | `ActionBody` → Cedar lowering과 Cedar 평가. WASM으로 컴파일되어 확장에 내장          |
| **policy-server (Rust/Axum)** | 인증, 지갑·계정 상태, 예측 델타(`/evaluate`), 정책 허브 API                           |
| **registryV2**                | 디코딩을 구동하는 어댑터 매니페스트 + 토큰 메타데이터 레지스트리(서명된 번들)                          |

## 행동 모델 (ActionBody)

정책의 입력은 계층형 **`ActionBody`** 트리입니다. `Action`은 `{ meta, body }` 구조로, `meta`는 제출 정보(`OnchainTx` 온체인 트랜잭션 vs `OffchainSig` 오프체인 서명)를, `body`는 도메인 변형을 담습니다.

```rust
pub enum ActionBody {
    Token(TokenAction),            // 전송, 승인, permit …
    Amm(AmmAction),                // 스왑, 유동성 …
    Lending(LendingAction),        // 예치, 차입, 상환, 청산
    Airdrop(AirdropAction),
    Launchpad(LaunchpadAction),
    Perp(PerpAction),              // 무기한선물 포지션, 레버리지
    LiquidStaking(LiquidStakingAction),
    Permission(PermissionAction),
    Yield(YieldAction),
    Restaking(RestakingAction),
    Staking(StakingAction),
    Governance(GovernanceAction),
    HyperliquidCore(HyperliquidCoreAction),
    Marketplace(MarketplaceAction),       // NFT(Seaport) 주문 서명·체결·취소
    Multicall { actions: Vec<ActionBody> }, // 묶음 호출 (예: Universal Router) — 재귀
    Unknown { /* target, chain, calldata, value */ }, // 미식별 — fail-closed
}
```

* 각 도메인은 `schema/policy-schema/actions/<domain>/` 아래에 대응하는 Cedar 스키마 조각을 가집니다.
* **`Multicall`** 은 재귀합니다 (Universal Router 실행이 중첩된 `ActionBody`로 분해됨).
* **`Unknown`** 은 어떤 어댑터도 인식하지 못한 호출의 fail-closed 분기입니다(기본: 경고/차단).

## 정책 RPC & remote facts

Cedar는 calldata에서 추출한 기본값에 더해, 선택적으로 **외부 사실(remote facts)** 을 함께 평가할 수 있습니다.

* 정책 번들은 `.policy-rpc.json` 매니페스트를 실어, 어떤 백엔드 메서드가 어떤 사실(예: USD 가치)을 공급하는지, 어떤 파라미터를 보내고 응답의 어느 필드를 Cedar 컨텍스트로 투영할지 선언합니다.
* 동작 순서: 확장이 WASM에 호출을 **plan**하게 함 → `policy-server`로 전송 → WASM이 응답을 **materialize**한 뒤 Cedar 평가.
* **엔진 자체는 오라클/잔고/허용량 조회를 직접 하지 않습니다.**

## 버전 이름 가이드

코드에서 만나는 버전 접미사는 "구/신"이 아니라 **프로토콜 이름**입니다.

| 이름                                                 | 의미                                                                                           |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `ActionBody` / **v3 decode**                       | `registryV2` 매니페스트 기반 선언형 디코더. "v3"은 디코드 레지스트리 포맷 이름                                         |
| **v2 evaluation**                                  | 무상태 per-request Cedar 평가(`{policy, manifest}` 번들 + 트리거 매칭 + `policy_rpc`). "v2"는 정책 번들 계약 이름 |
| **`ps2:*`**                                        | 확장의 정책 저장소 네임스페이스(계정 ⊃ 지갑 ⊃ 바인딩/패키지)이자 서비스 워커 메시지 패밀리                                        |
| `verdictSource: "declarative-v2" \| "fail_closed"` | 판정 출처: 실제 정책 평가 vs fail-closed 꼬리(미해석 요청·엔진 오류)                                              |

## 더 깊이

* 워크스페이스 레이아웃·빌드 방법: 코드 레포 루트의 `README.md`
* 레지스트리 서명/신뢰 체인: `registryV2/docs/REGISTRY_ARCHITECTURE.md`
* Cedar 스키마: `schema/policy-schema/core.cedarschema`
