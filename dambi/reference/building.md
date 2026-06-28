---
description: 로컬 빌드·실행과 기여 가이드
---

# 빌드 & 기여

DAMBI는 Rust + TypeScript 하이브리드 모노레포입니다. 이 페이지는 로컬 개발 시작점을 요약합니다. 항상 최신 정보는 코드 레포의 README들이에요.

## 워크스페이스 구성

| 경로 | 내용 |
|------|------|
| `browser-extension/` | Chrome MV3 / Firefox MV2 확장 (서비스 워커·콘텐츠 스크립트·팝업·확인 페이지·옵션 대시보드) |
| `browser-extension/dashboard/` | Vite + React 정책 관리 UI |
| `browser-extension/sdk/` | `@dambi/sdk` 확장 클라이언트 |
| `crates/policy-engine/` | `ActionBody` → Cedar lowering, Cedar 평가 |
| `crates/policy-engine-wasm/` | wasm-bindgen 브리지 (타입 `.d.ts` 포함) |
| `crates/policy-server/` | Axum 백엔드 (server/db/sync/asset-model) |
| `crates/adapters/` | 디코드 지원 (abi-resolver, mappers) |
| `registryV2/` | 어댑터 매니페스트·토큰 메타데이터·디코드 인덱스 빌드 |
| `schema/policy-schema/` | Cedar 스키마 (`core.cedarschema` + `actions/`) |

## Rust 워크스페이스

```bash
cargo test --workspace
cargo clippy --workspace --all-targets -- -D warnings
cargo fmt --all
```

* `scripts/test-all.sh` | 전체 스윕 (cargo test + clippy + fmt → 확장 타입체크/vitest/chrome 빌드)
* `scripts/lint.sh` | fix-everything (`cargo fmt` + `clippy --fix` + `yarn lint`)

> 디코드 하니스는 `registryV2/index/`(빌드 산출물, 미추적)를 읽습니다. 먼저 생성:
> ```bash
> cd registryV2 && npm ci && npm run build
> ```

## WASM 아티팩트

```bash
scripts/wasm-build.sh
```

`wasm-pack build crates/policy-engine-wasm --target web --release`를 돌려 산출물을 `browser-extension/backend/wasm/`와 `public/wasm/`로 복사합니다. 확장의 `yarn prepare:wasm`이 이를 호출합니다.

## 브라우저 확장

Yarn 4 워크스페이스. 빌드는 **두 파이프라인**(webpack + Vite)으로 `dist/<browser>/`에 들어갑니다.

```bash
cd browser-extension
yarn install
yarn build:ext         # chrome(webpack) + 대시보드(vite) → dist/chrome/
# 라이브 개발:
yarn dev:chrome        # webpack --watch (대시보드는 cd dashboard && yarn dev 병행)
```

빌드 타임 설정은 env 변수: `DAMBI_SERVER_URL`(기본 `http://127.0.0.1:8788`), `REGISTRY_BASE_URL`. 자세한 설치/로드는 [확장프로그램 설치하기](../getting-started/install.md) 및 `browser-extension/README.md`.

## policy-server (로컬)

```bash
cp .env.local.example .env.local       # JWT_SECRET, GOOGLE_*, DATABASE_URL …
scripts/start-policy-server.sh local
curl http://127.0.0.1:8788/readyz
```

최소 스택(Postgres :5544, Redis :6379)과 로컬 인증(개발 토큰 주입), prod-like minikube 루프는 `browser-extension/README.md`에 상세히 있습니다.

## CI

PR과 main push마다 CI가 fmt·clippy·test·wasm·확장 빌드·보안 스캔을 자동 검증합니다. 자세한 파이프라인 정의는 레포의 `.github/workflows/`(`ci.yml`, `dependency-policy.yml`)를 참고하세요.

## 오픈소스 기여

DAMBI는 오픈소스 프로젝트입니다. 누구나 기여할 수 있어요.

### 기여 흐름

1. **이슈 확인/생성** | 작업 전 관련 이슈가 있는지 확인하고, 없으면 새로 열어 의도를 공유합니다.
2. **포크/브랜치** | 저장소를 포크하거나 기능 브랜치를 만듭니다.
3. **변경 작성** | 주변 코드의 스타일·관례를 따릅니다.
4. **로컬 검증** | `scripts/test-all.sh`로 fmt·clippy·test·빌드를 모두 통과시킵니다.
5. **PR 생성** | 변경 의도와 영향을 설명합니다. CI(`ci.yml` + `dependency-policy.yml`)가 통과해야 머지됩니다.

### 기여하기 좋은 영역

* **새 프로토콜 어댑터** | `registryV2/manifests/<domain>/`에 매니페스트 추가 (서명된 번들 → [보안 모델](../security/security-model.md))
* **새 액션 도메인** | `asset-model/action`에 도메인 + `schema/policy-schema/actions/<domain>/` 스키마 + 디코더
* **정책 카탈로그** | 새 보호 정책/패키지 작성·검증
* **문서·번역** | 이 문서(GitBook) 개선, 한국어/영어 번역
* **버그 리포트** | 재현 절차와 함께 이슈로 제보

### 코딩 표준

* `cargo fmt` / `cargo clippy -D warnings` 통과
* 변경에는 테스트 추가, 기존 테스트 유지
* 확장은 `yarn typecheck` + vitest 통과

## 라이선스 & 저작권

* **프로젝트 라이선스** | DAMBI는 **Apache License 2.0** 으로 배포됩니다. 저장소 루트의 `LICENSE`(Apache-2.0 전문)가 공식 기준입니다.
* **재배포 시** | Apache-2.0 조건을 따릅니다:
  * **Apache-2.0 라이선스 사본**과 **`NOTICE` 파일**을 포함
  * **저작권·특허·상표·귀속(attribution) 고지** 유지
  * 파일을 수정했다면 **변경 사실 표시**
  * Apache-2.0은 기여자의 **특허 라이선스**를 함께 부여합니다
* **Cedar** | 정책 엔진이 기반하는 [Cedar](https://www.cedarpolicy.com/)(`cedar-policy`)도 Apache-2.0이며, 관련 고지는 `NOTICE`에 포함됩니다.
* **기여자 라이선스** | 별도 CLA가 없는 한, PR로 제출한 기여는 Apache-2.0 조건으로 배포되는 데 동의하는 것으로 간주됩니다.

## 다음 단계

* 내부 구조 → [아키텍처 개요](architecture.md)
* 레지스트리 서명 → [보안 모델](../security/security-model.md)
