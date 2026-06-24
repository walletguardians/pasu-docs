---
description: DAMBI가 인식하는 행동 도메인과 주요 액션
---

# 액션 종류 (도메인별 행동)

DAMBI는 raw 트랜잭션/서명을 도메인별 **행동(ActionBody)** 으로 디코드합니다. 정책은 이 도메인·액션 단위로 작성합니다. 아래는 각 도메인이 다루는 실제 지갑 행동입니다.

| 도메인 | 다루는 행동 | 주요 액션(tag) |
|--------|-------------|----------------|
| **Token** | ERC-20/721/1155 전송·승인·permit·취소, 네이티브 wrap/unwrap | `erc20_transfer`, `erc20_approve`, `nft_set_approval_for_all`, `permit2_sign_allowance`, `erc20_permit`, `revoke_approval`, `wrap_native`, `unwrap_native` |
| **Amm** | DEX 스왑, 유동성 추가/제거, 수수료 수취, 인텐트 주문 | `swap`, `add_liquidity`, `remove_liquidity`, `collect_fees`, `sign_intent_order`, `settle_intent_order`, `cancel_intent_order` |
| **Lending** | 예치·차입·상환·청산·담보 관리·운영자 위임 (Aave/Compound/Morpho/Spark) | `supply`, `withdraw`, `borrow`, `repay`, `liquidate`, `enable_collateral`, `set_e_mode`, `delegate_borrow`, `set_authorization` |
| **Airdrop** | 에어드롭 클레임, 투표권 위임 | `claim`, `delegate` |
| **Launchpad** | 토큰 세일 참여 (커밋·할당 클레임·환불) | `commit`, `claim_allocation`, `refund`, `withdraw_commit` |
| **Perp** | 무기한선물: 포지션 개·폐, 마진·레버리지 조정, 주문, 펀딩 (Hyperliquid/GMX/dYdX/Vertex/Drift …) | `open_position`, `close_position`, `change_leverage`, `adjust_margin`, `place_order`, `cancel_order`, `claim_funding` |
| **LiquidStaking** | 리퀴드 스테이킹 (Lido): stake·wrap·unwrap·출금 큐 | `stake`, `wrap`, `unwrap`, `request_withdrawal`, `claim_withdrawal` |
| **Permission** | 프로토콜 operator/relayer/manager 권한 부여·회수 | `grant_permission`, `revoke_permission` |
| **Yield** | 수익 토큰화 (Pendle V2): PT/YT 스왑·민트·리딤·유동성 | `swap`, `mint`, `redeem`, `add_liquidity`, `remove_liquidity`, `collect_rewards` |
| **Restaking** | 리스테이킹 (EigenLayer): 예치·위임·출금 큐 | `deposit`, `delegate`, `queue_withdrawal`, `complete_withdrawal`, `register_as_operator` |
| **Staking** | 락업/쿨다운 스테이킹 (veCRV, Aave Safety Module): lock·gauge·보상 | `lock`, `increase_lock_amount`, `vote_for_gauge`, `gauge_deposit`, `claim_rewards`, `cooldown`, `redeem` |
| **Governance** | DAO 거버넌스: 제안 생명주기(제안·투표·실행)·위임 | `propose`, `vote`, `queue`, `execute`, `delegate`, `cancel` |
| **HyperliquidCore** | Hyperliquid 오프체인 L1 주문·레버리지·출금 (`/exchange` 가로채기) | `place_order`, `change_leverage`, `hl_withdraw`, `hl_update_leverage` |
| **Marketplace** | NFT 거래 (Seaport): 주문 서명·체결·취소 | `sign_order`, `fulfill_order`, `cancel_order` |
| **Multicall** | 여러 행동의 원자적 묶음 (예: Uniswap Universal Router) | 바깥 묶음(`outer`) 또는 내부 각각(`inner`)에 정책 적용 |
| **Unknown** | 어떤 어댑터도 인식 못 한 호출 (대상·체인·raw calldata·value) | 기본 정책: 경고 또는 차단 (fail-closed) |

## Multicall 정책 작성

Universal Router 같은 묶음 호출은 중첩된 행동으로 분해됩니다. 정책 트리거의 `scope`로 적용 범위를 정할 수 있어요:

* `scope: "outer"` — 바깥 묶음 자체에 매칭
* `scope: "inner"` — 묶음 안 각 자식 행동을 따로 평가

## Unknown 처리

어떤 디코더도 인식하지 못한 호출은 `Unknown`이 되어 **fail-closed**(기본 경고/차단)로 처리됩니다. "모르면 일단 멈춘다"는 안전 장치예요. → [판정 이해하기](../getting-started/verdicts.md)

## 다음 단계

* 도메인별 실제 예제 → [유스케이스별 정책 예제](examples.md)
* 필드·스키마 상세 → [ActionBody & Cedar](../reference/actionbody-cedar.md)
