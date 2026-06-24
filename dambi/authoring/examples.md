---
description: 실제 내장·카탈로그 정책으로 보는 유스케이스별 예제
---

# 유스케이스별 정책 예제

DAMBI에 실제로 들어 있는 정책들을 유스케이스별로 정리했습니다. 그대로 설치하거나, 에디터에서 비슷하게 만들어 응용하세요.

## 🪙 토큰 승인 & 전송

### 무제한 승인 차단

* **무엇** — `erc20_approve`에서 한도가 `U256::MAX`(무제한)이면 차단
* **강도** — 🔴 `deny`
* **이유** — 무제한 승인은 스펜더가 잔고 전체를 인출할 수 있게 합니다. 필요한 만큼만 승인하세요.
* **트리거** — `action.tag == "erc20_approve"` / **검사** — `context.amount`가 U256 최대값

### 영(0) 주소로 전송 경고

* **무엇** — `erc20_transfer` 수신자가 `0x000…0`이면 경고 (되돌릴 수 없는 소각)
* **강도** — 🟡 `warn`
* **트리거** — `action.tag == "erc20_transfer"` / **검사** — `context.recipient == 0x0…0`

### Permit2 허용 서명 확인

* **무엇** — Permit2 토큰 허용 서명(EIP-712) 전에 경고
* **강도** — 🟡 `warn`
* **이유** — 스펜더와 금액을 확인하고 승인하세요.
* **트리거** — `action.tag == "permit2_sign_allowance"` (조건 없이 항상)

## 🔁 DEX / 스왑

### 스왑 금액 상한 (USD 기준)

* **무엇** — 특정 토큰을 일정 수량 이상 스왑하면 차단/경고
* **강도** — `deny` 또는 `warn`
* **동작** — 매니페스트가 `token.normalize_to_nano`로 금액을 정규화해 `context.custom`에 넣고, Cedar가 그 값과 토큰 주소를 검사
* **응용** — 큰 금액 스왑 경고, 슬리피지 과다 경고(`swap-high-slippage-warn`)

## 🏦 대출 (Lending)

### 운영자 권한 부여 확인

* **무엇** — 대출 프로토콜에서 다른 주소에 운영 권한을 부여(`set_authorization`)할 때 경고
* **강도** — 🟡 `warn` (`set-authorization-grant-confirm-warn`)
* **왜** — 권한 위임은 포지션 조작으로 이어질 수 있어 확인이 필요

## 📈 무기한선물 (Perp)

### 신규 숏 차단 (Hyperliquid)

* **무엇** — Hyperliquid에서 **신규** 숏 포지션 진입 차단
* **강도** — 🔴 `deny`
* **검사** — `venue == "hyperliquid" && side == "short" && reduceOnly == false` (포지션 청산은 오탐 안 나게 `reduceOnly`로 구분)

### 고레버리지 경고 (Hyperliquid)

* **무엇** — Hyperliquid에서 레버리지를 20배 초과로 올리면 경고
* **강도** — 🟡 `warn`
* **트리거** — `action.tag == "change_leverage"` / **검사** — `newLeverage > 20.0`

### 펀딩 출금 확인

* **무엇** — Hyperliquid 자금 출금 시 경고 (`HL-Confirm-Withdraw`)
* **강도** — 🟡 `warn`

## 패키지로 묶어 쓰기

위 정책들은 개별로도, **세트(패키지)** 로도 제공됩니다. 예: "DEX 안전 팩", "Hyperliquid 가드 팩"처럼 관련 정책을 한 번에 설치할 수 있어요. → [마켓플레이스](../user-guide/marketplace.md)

> 기본 내장(default) 세트에는 스왑 상한, 승인 보호, Hyperliquid 가드 등 12개 이상의 큐레이션 정책이 포함됩니다.

## 다음 단계

* 직접 만들기 → [에디터로 정책 만들기](editor.md)
* 동작 검증 → [시뮬레이션으로 테스트하기](../user-guide/simulation.md)
* 마켓에 올리기 → [마켓플레이스에 배포하기](publishing.md)
