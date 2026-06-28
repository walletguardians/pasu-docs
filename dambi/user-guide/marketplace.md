---
description: 정책 허브에서 정책·패키지를 찾아 설치하는 방법
---

# 정책 허브에서 정책 찾기·설치하기

정책 허브는 미리 만들어진 정책과 패키지를 찾아 설치하는 곳입니다. 직접 정책(Cedar 코드)를 쓰지 않고도 검증된 보호 규칙을 바로 적용할 수 있어요.

## 정책 vs 패키지

| 종류               | 설명                           |
| ---------------- | ---------------------------- |
| **정책 (policy)**  | 단일 규칙 (예: "무제한 승인 차단")       |
| **패키지(package)** | 여러 정책을 묶은 모음 (예: "DEX 안전 팩") |

### Market Home

* **공식 정책&패키지** | Wallet Guardians팀에서 제작한 제작한 111개의 정책 및 26개의 패키지
* **카테고리** | 행동별 카테고리 12가지
* **인기 정책** | 설치 횟수 기준 Top 10

### 목록 화면

* **검색** | 이름·해시태그 기반 검색
* **카테고리 선택** | 해시태그 기반 다중 선택 가능
* **정렬** | 인기순(popular) / 최신순(new) / 평점순(rating)

### 상세 페이지

정책 상세에서 확인할 수 있는 것:

* **게시자 등급(tier)** | Official(공식, ✓) / Verified / Community
* **설치 버튼** | `받기` 또는 이미 설치했다면 `설치됨`
* **문서** | 정의 / 범위 / 대상 / 데이터 (정책이 무엇을·어디까지·누구를 위해·어떤 데이터로 동작하는지)
* **정책 카드** | 강도(pass/fail/warn), 카테고리 태그, 한 줄 설명
* **평점** | 5점 기준의 평점

<figure><img src="../.gitbook/assets/image (17).png" alt="" width="364"><figcaption></figcaption></figure>

## 지갑 전용으로 받기

1. 상세 페이지에서 **받기(Install)** 클릭
2. 지갑 전용 설정으로 받기 선택
3. 정책이 설치될 지갑 및 패키지 선택
4. 정책에 빈칸(hole)이 있을 경우 파라미터 값 입력 가능
5. 정책이 에디터 및 해당 지갑의 패키지에 저장되고 사용 가능

<div><figure><img src="../.gitbook/assets/image (5).png" alt="" width="170"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (6).png" alt="" width="170"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (8).png" alt="" width="170"><figcaption></figcaption></figure></div>

## 라이브러리로 받기

1. 상세 페이지에서 **받기(Install)** 클릭
2. 라이브러리로 받기 선택
3. 정책 템플릿이 설치될 폴더 선택
4. 정책에 빈칸(hole)이 있을 경우 파라미터 값 입력 가능
5. 정책 템플릿이 에디터의 라이브러리에 저장되고 해당 템플릿을 자유롭게 수정해서 패키지에 적용 가능

## 평가·등급

* **리뷰 작성** | 설치한 버전에 평점을 남길 수 있습니다
* **게시자 등급** | 평가 평점 및 게시물 수에 따라 게시자 등급이 부여됩니다.

## 다음 단계

* 설치한 정책 켜고 끄기 → [정책 켜기·끄기와 지갑별 설정](managing-policies.md)
* 설치 전에 테스트 → [시뮬레이션으로 테스트하기](simulation.md)
* 직접 만들어 올리기 → [정책 허브에 배포하기](../authoring/publishing.md)
