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

`.github/workflows/ci.yml` (PR·main push마다):

* **rust** | registry 인덱스 빌드 → `fmt --check`, `clippy -D warnings`, `cargo test`(doctest 포함), `cargo doc -D warnings`
* **wasm** | `wasm-pack build` + 헤드리스 Chrome 테스트
* **extension** | `yarn typecheck`, vitest, Chrome MV3 빌드+zip, 대시보드 빌드
* **secrets-scan** | gitleaks

`dependency-policy.yml`(`cargo audit` + `cargo deny`)는 의존성 변경 시·주간 실행.

## 기여 방향

* **새 액션 도메인 추가** | `asset-model/action`에 도메인 + `schema/policy-schema/actions/<domain>/` 스키마 + 디코더 매니페스트
* **어댑터 매니페스트 추가** | `registryV2/manifests/<domain>/` (서명된 번들 → [보안 모델](../security/security-model.md))
* 코딩 표준 | `cargo fmt` / `clippy` 통과, 테스트 추가

## 다음 단계

* 내부 구조 → [아키텍처 개요](architecture.md)
* 레지스트리 서명 → [보안 모델](../security/security-model.md)
