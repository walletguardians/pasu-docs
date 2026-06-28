---
description: policy-server의 HTTP API 레퍼런스
---

# 서버 API

`policy-server`는 Rust/Axum 백엔드로, 인증·지갑 상태·예측 평가·정책 허브를 담당합니다. **정책 평가의 최종 판정은 확장프로그램(WASM)이 소유**하고, 서버는 상태 컨텍스트와 외부 사실을 제공합니다.

> 실행 중인 서버는 `/docs`(Swagger UI)와 `/openapi.yaml`(OpenAPI 3.0 스펙)을 제공합니다. 이 문서는 요약이고, 정확한 스키마는 그쪽이 최신입니다.

## 인증

* JWT(HS256), `JWT_SECRET`(≥32바이트)로 서명
* 토큰: **access**(~1시간), **refresh**(~30일), **oauth_state**(~5분, CSRF)
* 전달: `Authorization: Bearer <jwt>` 헤더 또는 `?token=<jwt>`(SSE)
* 클레임: `sub`(user_id), `email`, `typ`, `jti`, `iat`, `exp`

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `GET /auth/google` | - | Google 동의 화면으로 리다이렉트 (OAuth 시작) |
| `GET /auth/google/callback` | - | 콜백: code를 JWT로 교환, access는 URL fragment로 전달 |
| `POST /auth/refresh` | - | refresh 토큰 회전 (재사용 거부) |
| `GET /auth/me` | ✅ | 내 프로필(user_id, email) |

## 평가 (Evaluate)

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `POST /evaluate` | ✅ | 행동 envelope를 지갑 상태에 시뮬레이션 → 예측 StateDelta 반환 |

* **요청** | `wallet_id`(주소+체인), `envelopes`(Action[]), `eval_context`(체인·시각·kind), `call_specs`(enrichment 호출)
* **응답** | `policyRequest`(`actions`, `state_before`, `deltas`, `state_after`, `results`) + `diagnostics`
* `call_specs`는 `oracle.usd_value` 같은 외부 사실 호출. 결과는 `call_id`로 키잉되어 확장이 materialize.
* 예측 상태는 **메모리상 계산이며 영속화되지 않음** (영속화는 `/wallets/:addr/sync`만).

## 지갑 (Wallets)

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `GET /wallets` | ✅ | 내 지갑 목록 |
| `POST /wallets` | ✅ | 지갑 등록 (초기 상태 동기화 실행) |
| `PATCH /wallets/:address` | ✅ | 라벨·owned·archived 수정 |
| `DELETE /wallets/:address` | ✅ | 지갑 삭제(소프트) |
| `POST /wallets/:address/sync` | ✅ | 상태 새로고침 (RPC/오라클, 분산 락) |
| `POST /wallets/:address/permits` | ✅ | 서명된 ERC-2612/Permit2 permit 수집 |
| `GET /wallets/:address/state` | ✅ | 전체 상태 (보유·승인·포지션) |
| `GET /wallets/:address/holdings` | ✅ | 보유 토큰 |
| `GET /wallets/:address/approvals` | ✅ | 승인 목록 |
| `GET /wallets/:address/positions` | ✅ | 대출/차입 포지션 |
| `GET /wallets/:address/pending` | ✅ | 대기 트랜잭션 |
| `GET /wallets/:address/block-heights` | ✅ | 체인별 마지막 동기화 블록 |

## 토큰 · 대시보드

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `GET /tokens` | ✅ | 전역 토큰 카탈로그 (메타데이터) |
| `GET /dashboard/summary` | ✅ | 전 지갑 집계 요약 (자산·부채·헬스) |

## 정책 허브 (Market)

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `GET /market/listings` | ✅ | 목록 (kind/domain/category/publisher/q/sort, limit≤100) |
| `GET /market/activity-summary` | ✅ | 최근 설치 활동 (인기 추천용) |
| `GET /market/listings/:slug` | ✅ | 상세 (요약+최신 버전+최근 리뷰) |
| `GET /market/listings/:id/versions/:version` | ✅ | 특정 버전 본문 (설치용) |
| `GET /market/listings/:id/reviews` | ✅ | 리뷰 목록 |
| `POST /market/listings` | ✅ | 게시 (초기 버전 1.0.0) |
| `POST /market/listings/:id/versions` | ✅ | 새 SemVer 버전 게시 (게시자) |
| `POST /market/listings/:id/install` | ✅ | 설치 기록 + 버전 본문 반환 |
| `POST /market/listings/:id/reviews` | ✅ | 리뷰 작성/교체 (rating 1~5) |
| `POST /market/listings/:id/report` | ✅ | 정책 신고 |
| `POST /market/reviews/:id/report` | ✅ | 리뷰 신고 |
| `POST /market/reviews/:id/helpful` | ✅ | 리뷰 도움됨 투표 |
| `POST` / `DELETE /market/listings/:id/watch` | ✅ | 워치 추가/제거 |
| `GET /market/watches` | ✅ | 내 워치 목록 |
| `GET /market/reports/mine` | ✅ | 내가 낸 신고 |
| `GET /market/reports` · `PATCH /market/reports/:id` | ✅ (admin) | 모더레이션 큐·상태 변경 |
| `DELETE /market/listings/:id` | ✅ | 정책 삭제 (게시자) |

## 이벤트 · 헬스 · 문서

| 메서드·경로 | 인증 | 설명 |
|-------------|------|------|
| `GET /events/stream` | ✅ | SSE 실시간 피드 (wallet_sync, tx_confirmed 등) |
| `GET /health` | - | 라이브니스 (텍스트 `ok`) |
| `GET /readyz` | - | 레디니스 (DB/JWT/OAuth/Redis/sync 점검) |
| `GET /docs` · `GET /openapi.yaml` | - | Swagger UI · OpenAPI 스펙 |

## 참고 사항

* **체인 ID는 CAIP-2** 형식 (`eip155:1`, `eip155:42161`, `eip155:8453`)
* **정책·판정은 서버에 저장되지 않음** | 서버는 지갑 상태·토큰 메타·정책 허브 데이터만 보관
* 외부 사실(enrichment)은 best-effort, optional은 실패해도 평가를 막지 않음

## 다음 단계

* 평가 모델 상세 → [ActionBody & Cedar](actionbody-cedar.md)
* 데이터 흐름·프라이버시 → [데이터 흐름](../security/data-flows.md)
