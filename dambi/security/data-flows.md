---
description: 어떤 데이터가 기기에 남고, 무엇이 언제 서버로 가는지
---

# 데이터 흐름

DAMBI의 설계 원칙은 **"판정은 기기 안에서, 데이터는 최소한으로"** 입니다. 이 페이지는 데이터가 정확히 어디에 머물고 언제 서버로 가는지 보여줍니다.

## 한눈에

```mermaid
flowchart TD
    subgraph 기기[기기(확장프로그램)]
      P[정책 ps2:*]
      V[판정 히스토리]
      S[설정·OpenAI 키]
      W[WASM 평가]
    end
    subgraph 서버[policy-server]
      A[인증·이메일]
      ST[지갑 상태]
      F[remote facts 실행]
    end
    W -->|"필요 시"| F
    기기 -.->|"로그인·동기화 시"| 서버
```

## 기기에만 남는 데이터 (서버 전송 없음)

| 데이터 | 저장소 |
|--------|--------|
| 설치한 정책·정의 | `chrome.storage.local` (`ps2:*`) |
| 판정 히스토리 (최대 1000건) | `chrome.storage.local` (`verdicts:log`) |
| 설정 (서버 주소 등) | `chrome.storage.local` |
| **OpenAI API 키** | 브라우저 `localStorage` (서버로 절대 전송 안 함) |
| 인증 토큰 | `chrome.storage.local` (`dambi_jwt`, `dambi_jwt_refresh`) |
| 진행 중 요청 | `chrome.storage.session` (재시작 시 사라짐) |

## 서버로 가는 데이터 (`/evaluate` 등)

상태 기반 평가나 예측을 쓸 때 다음이 전송됩니다.

| 데이터 | 내용 |
|--------|------|
| 지갑 식별자 | 주소 + 추적 체인 |
| **디코드된 행동** | `ActionBody` (raw calldata가 아니라 **해석된 구조**) |
| 지갑 상태 | 이전 동기화로 얻은 잔고·승인·포지션 (`state_before`) |
| enrichment 파라미터 | 디코드된 행동에서 추출한 토큰 주소·체인·금액 등 (`call_specs.params`) |
| 인증 이메일 | OAuth subject 기준으로 사용자 DB에 저장 |

## 절대 전송하지 않는 것

* 🔒 raw 트랜잭션 calldata 16진수 (디코드된 구조만 전송)
* 🔒 개인키·시드 문구·니모닉
* 🔒 OpenAI API 키 (브라우저에만)
* 🔒 판정·정책 감사 로그 (기기 로컬에만)

## 정책 평가는 기본적으로 오프라인

트랜잭션/서명 **판정은 기기 안 WASM에서** 끝납니다. 네트워크 왕복이 필요 없어요. 서버는 다음 경우에만 개입합니다:

1. **상태 기반 정책** | USD 가치 같은 외부 사실이 필요할 때 (`policy_rpc` → `/evaluate`)
2. **예측 델타·자산 화면** | 지갑 상태 동기화
3. **인증·정책 허브** | 로그인, 정책 게시/리뷰/설치

> remote fact가 필요할 때도 **해석된 파라미터만** 갑니다. 예: USD 가치 조회 시 `{ chain, token, amount }`만 전송하고, raw calldata는 다시 보내지 않습니다. → [ActionBody & Cedar](../reference/actionbody-cedar.md)

## 제3자

| 서비스 | 용도 | 비고 |
|--------|------|------|
| Google OAuth | 로그인 | 이메일 + provider subject만 저장 |
| 레지스트리 (registryV2 / registry-api) | 디코드용 어댑터·토큰 메타 | 서명 검증 → [보안 모델](security-model.md) |
| CoinGecko | 토큰 메타데이터 | best-effort, 실패해도 무방 |
| Etherscan (선택) | 지갑 토큰 탐색 | 키 없으면 네이티브만 |
| Chainlink | 가격 폴백 | 1.5s 타임아웃, fail-open |
| Chainalysis (선택) | 제재 주소 검사 | 1.5s 타임아웃, fail-open |
| Alchemy (선택) | NFT 바닥가 | optional |
| OpenAI (선택) | LLM 정책 초안 | **브라우저에서 직접 호출**, 키는 로컬 |

## 다음 단계

* 신뢰 모델·서명 → [보안 모델](security-model.md)
* 법적 처리방침 → [개인정보 처리방침](privacy.md)
