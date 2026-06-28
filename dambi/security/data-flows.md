---
description: 어떤 데이터가 기기에 남고, 무엇이 언제 서버로 가는지
---

# 데이터 흐름

DAMBI는 **"판정은 기기 안에서, 데이터는 최소한으로"** 를 원칙으로 합니다.

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

## 기기에만 남는 데이터

| 데이터                | 저장소                                     |
| ------------------ | --------------------------------------- |
| 설치한 정책·정의          | `chrome.storage.local` (`ps2:*`)        |
| 판정 히스토리 (최대 1000건) | `chrome.storage.local` (`verdicts:log`) |
| 설정·인증 토큰           | `chrome.storage.local` (`dambi_jwt` 등)  |
| OpenAI API 키       | 브라우저 `localStorage`                     |

## 서버로 가는 데이터 (`/evaluate` 등)

| 데이터             | 내용                                      |
| --------------- | --------------------------------------- |
| 지갑 식별자          | 주소 + 추적 체인                              |
| **디코드된 행동**     | `ActionBody` (raw calldata가 아니라 해석된 구조) |
| 지갑 상태           | 동기화로 얻은 잔고·승인·포지션                       |
| enrichment 파라미터 | 디코드된 행동에서 추출한 토큰·체인·금액 등                |
| 계정 이메일          | 인증용                                     |

**절대 전송하지 않음**: 🔒 raw calldata 16진수, 개인키·시드 문구, OpenAI 키, 판정·정책 로그(기기 로컬).

## 평가는 기본적으로 오프라인

판정은 기기 안 WASM에서 끝나 네트워크 왕복이 필요 없습니다. 서버는 ① 상태 기반 정책(외부 사실), ② 예측·자산 화면(지갑 동기화), ③ 인증·정책 허브에만 개입합니다. remote fact가 필요할 때도 **해석된 파라미터만**(예: `{ chain, token, amount }`) 보내고 raw calldata는 다시 전송하지 않습니다. → [ActionBody & Cedar](../reference/actionbody-cedar.md)

## 다음 단계

* 신뢰 모델·서명 → [보안 모델](security-model.md)
* 제3자 목록·처리방침 → [개인정보 처리방침](privacy.md)
