---
description: 설치한 정책을 지갑별로 켜고 끄고 관리하기
---

# 정책 켜기·끄기와 지갑별 설정

설치한 정책은 **지갑별로** 켜고 끌 수 있습니다. 즉 같은 정책을 어떤 지갑엔 적용하고 다른 지갑엔 빼는 식으로 운용할 수 있습니다. 정책은 패키지 단위로 관리할 수 있고, 패키지 안의 정책을 on/off시킬지 선택할 수 있습니다.

## On / Off

* **정책 관리(Editor)** 또는 **팝업**의 정책 카탈로그에서 각 정책을 켜고 끌 수 있습니다.
* Home 화면에서 관리할 수 있습니다. 패키지 토글을 통해 정책 활성화가 가능하고 정책 개별 on/off 가능

<div><figure><img src="../.gitbook/assets/image (11).png" alt="" width="215"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (12).png" alt="" width="215"><figcaption></figcaption></figure></div>

* 에디터 화면에서도 지갑별로 정책 on/off 토글을 제공합니다

## 강도(severity) 확인&#x20;

각 정책에는 강도가 표시됩니다:

| 강도            | 동작            |
| ------------- | ------------- |
| 🔴 `deny(차단)` | 서명/전송 막힘      |
| 🟡 `warn(경고)` | 사용자가 진행/취소 선택 |
| ℹ️ `info`     | 정보성 알림        |

자세한 의미 → [판정 이해하기](../getting-started/verdicts.md)

## 정책 편집·삭제

* **편집** | 정책 관리에서 해당 정책을 열어 조건·값·강도·설명을 수정 ([에디터](../authoring/editor.md))
* **삭제** | 더 이상 필요 없는 사용자 정책 제거 (기본 내장 정책은 삭제 불가)
* **초기화** | 프로필 → 설정에서 정책 전체 초기화 (기본 내장 제외)

## 지갑 추가·관리

지갑 상태 기반 기능(자산 모니터링, 시뮬레이션)을 쓰려면 지갑을 등록하세요.

* 지갑 등록 시 초기 상태 동기화가 실행됩니다 (보유 토큰·승인·포지션)
* 라벨 지정, 보관(archive) 가능
* 상태 새로고침은 수동 동기화로 갱신

<div><figure><img src="../.gitbook/assets/image (13).png" alt="" width="188"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (14).png" alt="" width="375"><figcaption></figcaption></figure></div>

> 정책 평가 자체는 지갑 등록 없이도 동작합니다. 등록은 **상태 기반 정책**(예: USD 가치 기준)과 자산 화면을 위한 것이에요.

## 다음 단계

* 정책 동작 검증 → [시뮬레이션으로 테스트하기](simulation.md)
* 판정 기록 보기 → [히스토리 & 감사 로그](history.md)
