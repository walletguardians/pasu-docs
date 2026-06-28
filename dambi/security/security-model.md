---
description: 기기 내 평가, 레지스트리 서명, fail-closed, 인증 보안
---

# 보안 모델

DAMBI의 보안은 네 기둥 위에 섭니다: **기기 내 평가**, **서명된 레지스트리**, **fail-closed 기본값**, **안전한 인증**.

## 1. 기기 내 평가 (no round-trip)

* 트랜잭션/서명 판정은 서비스 워커 안 **WASM Cedar 엔진**이 내립니다.
* 지갑에 응답을 돌려보내기 전에 판정이 로컬에서 결정됩니다 (네트워크 왕복 불필요).
* 따라서 서버가 다운되거나 느려도 보호의 핵심은 계속 동작합니다.

## 2. fail-closed (모르면 멈춘다)

확실히 판단할 수 없을 때는 안전한 쪽으로 닫습니다.

| 상황 | 처리 |
|------|------|
| 디코드 불가 (어댑터 미인식) | `ActionBody::Unknown` → 경고/차단 |
| v2 번들 매칭 0건 / plan 실패 | fail-closed |
| 엔진 오류·하드 타임아웃(8s) | fail-closed |

`verdictSource`로 출처를 구분합니다: `declarative-v2`(실제 평가) vs `fail_closed`(안전 꼬리). fail-closed 판정은 트랜잭션/서명은 **WARN**, venue 주문은 **DENY**. → [판정 이해하기](../getting-started/verdicts.md)

## 3. 레지스트리 서명 (디코더 무결성)

디코딩을 구동하는 `registryV2` 번들은 위변조를 막기 위해 서명됩니다.

* **알고리즘** | ECDSA P-256 + SHA-256 (`EC_SIGN_P256_SHA256`)
* **서명** | Cloud KMS(HSM 보호, 키 추출 불가)로 정규화된(RFC 8785 JCS) 번들 해시에 서명. 사이드카 `signatures/<sha256>.sig`에 보관.
* **검증** | 확장이 번들 설치 **전에** 검증:
  1. 받은 번들을 직접 해시 → `localSha`
  2. 응답이 주장한 해시와 일치 확인 (공격자 주장값은 서명 조회에 안 씀)
  3. `localSha`로 서명 조회
  4. WebCrypto로 ECDSA 검증
* **핀 키(pinned key)** | 공개키는 빌드 타임에 확장 소스에 주입(채널별). 알고리즘·키가 하드코딩이라 악성 레지스트리가 `alg:"none"`으로 다운그레이드 불가.
* **fail-closed** | 검증 실패 시 디코더 설치를 막습니다 (tx는 warn-closed, 타입 서명은 null 반환).

> **신뢰 앵커는 레지스트리 바깥(확장 내부)에 있습니다.** 따라서 레지스트리가 털려도 확장이 받아들일 번들을 위조할 수 없습니다.

구현: `registryV2/docs/REGISTRY_ARCHITECTURE.md`, `registryV2/scripts/sign-bundles.ts`, `browser-extension/backend/service-worker/adapter-loader/bundle-verify.ts`

## 4. 인증 보안

* **JWT (HS256)** | `JWT_SECRET`(≥32바이트)로 서명. 클레임: `sub`, `email`, `jti`, `typ`, `iat`, `exp`.
* **토큰 수명** | access ~1시간, refresh ~30일, oauth_state ~5분.
* **OAuth CSRF** | state JWT + HttpOnly 쿠키 nonce로 콜백 검증.
* **토큰 전달** | access는 OAuth 콜백 시 **URL fragment**(`#access_token=…`)로 전달돼 서버 로그·Referer에 안 남음. refresh는 허용 목록 리다이렉트(확장)에만 발급.
* **회전** | refresh는 회전식이며 재사용은 거부(DB의 jti 추적).

## LLM 정책 초안의 안전장치

* OpenAI 호출은 **브라우저에서 직접** 이뤄지고 키는 로컬에만 저장 (서버 경유 없음).
* LLM은 Cedar를 직접 쓰지 않습니다. 폼 모델 초안을 만들고, 그 결과를 **WASM Cedar 엔진이 검증**합니다.

## 알려진 한계 (정직하게)

| 항목 | 상태 |
|------|------|
| 번들 **신선도(freshness)** | 서명은 진본성은 보장하나 최신성은 아님: 진짜였던 구버전으로의 롤백 가능성. TUF 스타일 버전닝 검토 중. |
| 콜드스타트 저장소 변조 | 검증된 캐시 번들을 신뢰: 직접 저장소 쓰기(레지스트리 MITM보다 강한 공격자)는 범위 밖. |
| 핀 키 회전 | 현재 듀얼핀 회전은 확장 릴리스 2회 필요. |

## 취약점 제보

> 🔧 **TODO** | 보안 취약점 제보 채널(이메일/폼)과 책임 있는 공개(responsible disclosure) 정책을 명시하세요.

## 다음 단계

* 데이터가 어디로 가나 → [데이터 흐름](data-flows.md)
* 법적 처리방침 → [개인정보 처리방침](privacy.md)
