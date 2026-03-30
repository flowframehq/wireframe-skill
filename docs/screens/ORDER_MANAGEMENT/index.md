---
screenId: ORDER_MANAGEMENT
title: 주문 관리
purpose: 쇼핑몰 관리자가 주문 현황을 조회하고, 주문 상태 변경/배송 처리/환불/취소를 수행하는 관리 화면
viewport: pc
---

# 주문 관리

## 레이아웃

1. 상단 영역 — 페이지 제목, 주문 통계 요약 (오늘 신규 주문, 처리 대기 건수 등)
2. 메인 영역 — [@order-list](../../features/order-list/index.md)
3. 상세 드로어 — [@order-detail](../../features/order-detail/index.md)
   - [@order-actions/status-change](../../features/order-actions/status-change/index.md)
   - [@order-actions/shipping](../../features/order-actions/shipping/index.md)
   - [@order-actions/refund-cancel](../../features/order-actions/refund-cancel/index.md)
   - [@order-memo](../../features/order-memo/index.md)

## 화면 연계 인수조건

- Given 주문 목록에서 행을 클릭하면 Then 우측에 주문 상세 드로어가 열리고 해당 주문의 상세 정보, 상태 변경, 배송 처리, 환불/취소, 메모 영역이 표시된다
- Given 상태 변경에서 주문 상태를 변경하면 Then 주문 목록 테이블의 해당 행 주문상태 뱃지가 즉시 갱신된다
- Given 배송 처리에서 송장번호를 등록하면 Then 주문 목록의 배송상태가 "배송중"으로 갱신되고, 주문 상세의 배송지 정보 영역에 추적 링크가 표시된다
- Given 환불/취소를 처리하면 Then 상태 변경 영역의 버튼이 비활성화되고, 주문 목록과 상세의 상태가 동시에 갱신된다
- Given 드로어가 열린 상태에서 다른 주문 행을 클릭하면 Then 드로어 내용이 새로 클릭한 주문으로 전환된다
