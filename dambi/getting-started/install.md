---
description: Chrome / Firefox에 DAMBI 확장프로그램을 설치하는 방법
---

# 확장프로그램 설치하기

DAMBI는 Chrome(MV3) / Firefox(MV2) 확장프로그램입니다. 설치 방법은 **두 가지**입니다.

* **일반 사용자** — 웹스토어에서 설치 (권장)
* **개발자/테스터** — 소스에서 빌드해 압축 해제(unpacked) 상태로 로드

---

## 1. 웹스토어에서 설치 (일반 사용자)

> 🔧 **TODO** — 정식 등록 후 스토어 링크를 채워주세요.

1. [Chrome 웹스토어 — DAMBI](https://chrome.google.com/webstore) 페이지를 엽니다. *(등록 예정)*
2. **Chrome에 추가**를 누릅니다.
3. 설치가 끝나면 브라우저 우측 상단 확장프로그램 아이콘 목록에 DAMBI가 나타납니다. 📌 핀으로 고정해 두면 편합니다.

설치 직후엔 기본 정책만 켜져 있습니다. 더 보호받으려면 [5분 튜토리얼](tutorial.md)을 따라 마켓에서 정책을 받아보세요.

---

## 2. 소스에서 빌드해서 설치 (개발자/테스터)

코드를 직접 빌드해 압축 해제 상태로 로드하는 방법입니다. 자세한 내용은 코드 레포의 `browser-extension/README.md`를 참고하세요.

### 사전 준비

* Node.js + Yarn 4
* Rust 툴체인 (WASM 빌드용)

### 빌드

```bash
cd browser-extension
yarn install
yarn build:ext        # chrome(webpack) + 대시보드(vite) → dist/chrome/
```

> `yarn build:ext`는 webpack(서비스 워커·콘텐츠 스크립트·팝업·확인 페이지)을 먼저 돌린 뒤, Vite로 옵션 페이지(대시보드)를 빌드합니다. 이 **순서가 중요**합니다. (`yarn dev:chrome`만 돌리면 옵션 페이지가 빠져 Chrome이 로드를 거부합니다.)

### 로드

1. 주소창에 `chrome://extensions` 입력
2. 우측 상단 **개발자 모드** 켜기
3. **압축 해제된 확장 프로그램을 로드합니다** → `browser-extension/dist/chrome` 폴더 선택

확장 ID는 manifest의 `key` 덕분에 항상 동일하게 유지됩니다.

---

## 처음 켰을 때 보이는 화면

| 진입점 | 설명 |
|--------|------|
| **팝업** (확장 아이콘 클릭) | 정책 카탈로그(켜기/끄기) + **Google 로그인** |
| **대시보드** (옵션 페이지) | 정책 만들기·관리, 마켓플레이스, 시뮬레이션, 히스토리 등 전체 관리 화면. 팝업 → **대시보드 열기** 로 진입 |

## (선택) 로그인 / 서버 연결

마켓플레이스, 지갑 상태 동기화, 예측 결과 같은 기능은 백엔드(`policy-server`)와 연결됩니다.

* **Google 로그인** — 팝업 또는 대시보드에서 *Sign in with Google*. 한 번 로그인하면 서비스 워커(트랜잭션 평가)와 대시보드 모두에 적용됩니다.
* **서버 주소 바꾸기** — 대시보드 → **Settings**에서 런타임으로 변경 가능합니다(재빌드 불필요). 로컬 개발용 기본값은 `http://127.0.0.1:8788`입니다.

> 💡 **정책 평가 자체는 서버 없이도 동작합니다.** 판정은 기기 안 WASM에서 끝나고, 서버는 인증·지갑 상태·예측 같은 부가 정보만 제공합니다. 자세한 건 [아키텍처 개요](../reference/architecture.md)를 보세요.

## 동작 확인 (스모크 테스트)

dApp에서 스왑이나 서명을 한 번 시도한 뒤, 서비스 워커 콘솔(`chrome://extensions` → 해당 확장의 *service worker* → inspect)에서 다음 로그가 찍히는지 확인하세요.

```
[Dambi] tx { hostname, chainId, to, data, bypassed }
[Dambi] typed-sig { hostname, chainId, primaryType, bypassed }
[Dambi] personal-sign { hostname, messageLen, bypassed }
```

## 다음 단계

{% content-ref url="tutorial.md" %}
[5분 튜토리얼](tutorial.md)
{% endcontent-ref %}
