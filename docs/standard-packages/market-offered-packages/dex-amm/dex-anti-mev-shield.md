---
description: 큰 금액을 스왑하는 일이 잦은 트레이더를 위한
---

# \[DEX] 예상보다 큰 거래 비용 보호

거래 규모가 커지면, 슬리피지, Price Impact, MEV에 따른 손실도 함께 커집니다. 따라서 규모가 큰 거래, 혹은 커질 수 있는 거래에 대해 서명 전에 미리 영향을 파악하고, 이에 따라 사용자의 판단이 필요한 시점에 경고를 표시합니다.

### 이 패키지로 막을 수 있는 것

* 지불 상한이 무제한인 exact-output Swap을 하는 상황
* 한 번에 지나치게 큰 금액을 Swap 하는 상황
* 보유량의 절반 이상을 한 번에 Swap하는 상황
* Price Impact이 큰(5% 초과) Swap을 하는 상황
* 악성으로 알려진 라우터나 풀을 통한 Swap을 하는 상황

### 정책 목록

* 지불 상한이 무제한인 Swap 요청 시 경고 (AMM-005)
* 일정 금액을 초과하는 Swap 요청 시 경고 (AMM-007)
* 보유량의 절반을 초과하는 자산을 한 번에 Swap할 시 경고 (AMM-008)
* Price Impact가 기준(5%)을 초과하는 Swap 요청 시 경고 (AMM-009)
* 악성으로 알려진 라우터나 풀을 통한 Swap 요청 시 차단 (AMM-013)

***

**\[DEX] Anti-MEV Shield**\
Wallet Guardians | v.1.0.0 | 26/06/11\
\
&#xNAN;_&#x53;upported Chain: Ethereum_
