---
description: 기기 내 평가, 레지스트리 서명, fail-closed, 인증 보안
---

# 보안 모델

DAMBI 보안의 네 기둥: **기기 내 평가**, **서명된 레지스트리**, **fail-closed**, **안전한 인증**.

## 기기 내 평가

트랜잭션/서명 판정은 서비스 워커 안 **WASM Cedar 엔진**이 내립니다. 지갑에 응답을 돌려보내기 전에 로컬에서 결정되므로(네트워크 왕복 불필요), 서버가 느리거나 다운돼도 보호의 핵심은 동작합니다.

## fail-closed (모르면 멈춘다)

| 상황 | 처리 |
|------|------|
| 디코드 불가(어댑터 미인식) | `Unknown` → 경고/차단 |
| 매칭 0건 / plan 실패 / 엔진 오류 / 타임아웃(8s) | fail-closed |

판정 출처는 `declarative-v2`(실제 평가)와 `fail_closed`(안전 꼬리)로 구분되며, fail-closed는 tx/서명은 **WARN**, venue 주문은 **DENY** 입니다. ([판정 이해하기](../getting-started/verdicts.md))

## 레지스트리 서명 (디코더 무결성)

디코딩을 구동하는 `registryV2` 번들은 **ECDSA P-256 + SHA-256**으로 서명됩니다(Cloud KMS, HSM 보호). 확장은 번들 설치 전에 직접 해시를 다시 계산해 서명을 검증하고, 공개키는 빌드 타임에 확장에 핀(pin)됩니다. 검증 실패 시 디코더 설치를 막습니다.

> **신뢰 앵커가 레지스트리 바깥(확장 내부)에 있어**, 레지스트리가 털려도 확장이 받아들일 번들을 위조할 수 없습니다. 구현: `registryV2/docs/REGISTRY_ARCHITECTURE.md`, `scripts/sign-bundles.ts`, `adapter-loader/bundle-verify.ts`

## 인증 보안

* **JWT (HS256)** | access ~1시간 / refresh ~30일 / oauth_state ~5분.
* OAuth는 state JWT + HttpOnly 쿠키 nonce로 CSRF를 막고, access 토큰은 **URL fragment**로 전달돼 서버 로그에 남지 않습니다. refresh는 회전식(재사용 거부).

## LLM 초안의 안전장치

OpenAI 호출은 **브라우저에서 직접** 이뤄지고 키는 로컬에만 저장됩니다(서버 경유 없음). LLM은 Cedar를 직접 쓰지 않고 폼 모델 초안만 만들며, 결과는 **WASM Cedar 엔진이 검증**합니다.

## 알려진 한계

* **번들 신선도**: 서명은 진본성은 보장하나 최신성은 아님(구버전 롤백 가능성). TUF 스타일 버전닝 검토 중.
* **콜드스타트 저장소 변조**: 검증된 캐시 번들을 신뢰(직접 저장소 쓰기는 범위 밖).
* **핀 키 회전**: 현재 듀얼핀 회전은 확장 릴리스 2회 필요.

## 취약점 제보

보안 취약점은 **GitHub Security Advisories** ([walletguardians](https://github.com/walletguardians))로 비공개 제보해 주세요. 공개 이슈로 올리기 전에 먼저 제보 부탁드립니다(책임 있는 공개).

## 다음 단계

* 데이터가 어디로 가나 → [데이터 흐름](data-flows.md)
* 처리방침 → [개인정보 처리방침](privacy.md)
