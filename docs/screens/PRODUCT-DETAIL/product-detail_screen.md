---
screenId: PRODUCT-DETAIL
title: 상품 상세
purpose: 상품의 상세 정보를 확인하고 옵션을 조합 선택하여 장바구니 담기 또는 바로 구매로 전환하는 페이지
viewport: [pc, mobile]
features: [PRODUCT, CART, ORDER, REVIEW, QNA]
---

## Screen

### 레이아웃 (PC)

1. 좌측 이미지 영역 — @PRODUCT/IMAGE_GALLERY
2. 우측 구매 영역 상단 — @PRODUCT/INFO
3. 우측 구매 영역 옵션 — @PRODUCT/OPTION
4. 우측 구매 영역 수량 — @PRODUCT/QUANTITY
5. 우측 구매 영역 액션 — @CART/ADD_TO_CART, @ORDER/BUY_NOW
6. 하단 탭: 상세정보 — @PRODUCT/DETAIL_DESCRIPTION
7. 하단 탭: 리뷰 — @REVIEW/RATING_SUMMARY, @REVIEW/REVIEW_LIST
8. 하단 탭: 문의 — @QNA/QNA_LIST

### 레이아웃 (Mobile)

1. 상단 이미지 영역 — @PRODUCT/IMAGE_GALLERY
2. 상품 정보 영역 — @PRODUCT/INFO
3. 옵션 선택 영역 — @PRODUCT/OPTION
4. 수량 및 합계 영역 — @PRODUCT/QUANTITY
5. 액션 버튼 영역 (하단 고정) — @CART/ADD_TO_CART, @ORDER/BUY_NOW
6. 탭: 상세정보 — @PRODUCT/DETAIL_DESCRIPTION
7. 탭: 리뷰 — @REVIEW/RATING_SUMMARY, @REVIEW/REVIEW_LIST
8. 탭: 문의 — @QNA/QNA_LIST

## Requirement

### 상품 정보 확인 — @PRODUCT/INFO

- Given 상품 상세 페이지 진입 When 페이지 로드 완료 Then 상품명, 판매가, 배송 정보가 표시된다
- Given 할인이 적용된 상품 When 페이지 로드 완료 Then 할인가와 할인율이 판매가와 함께 표시된다
- Given 품절 상품 When 페이지 로드 완료 Then 품절 상태가 표시된다

### 이미지 탐색 — @PRODUCT/IMAGE_GALLERY

- Given 상품에 복수 이미지 등록 When 썸네일 클릭 Then 해당 이미지가 대표 이미지 영역에 표시된다
- Given 페이지 최초 진입 When 이미지 갤러리 로드 완료 Then 첫 번째 이미지가 대표 이미지로 표시된다

### 옵션 조합 선택 — @PRODUCT/OPTION

- Given 옵션이 존재하는 상품 When 모든 필수 옵션 그룹 선택 완료 Then 선택된 옵션 조합이 선택 목록에 추가된다
- Given 선택된 옵션 목록에 항목 존재 When 옵션 삭제 버튼 클릭 Then 해당 옵션 조합이 목록에서 제거된다
- Given 특정 옵션 조합이 품절 When 해당 옵션 선택 시도 Then 해당 옵션이 비활성화 상태로 표시된다

### 수량 조정 — @PRODUCT/QUANTITY

- Given 선택된 옵션 존재 When 수량 증가 버튼 클릭 Then 수량이 1 증가하고 합계 금액이 재계산된다
- Given 수량이 1 When 수량 감소 버튼 클릭 Then 수량 감소 버튼이 비활성화 상태를 유지한다

### 장바구니 담기 — @CART/ADD_TO_CART

- Given 옵션 선택 완료 When 장바구니 담기 버튼 클릭 Then 선택된 옵션과 수량이 장바구니에 추가되고 완료 안내가 표시된다
- Given 필수 옵션 미선택 When 장바구니 담기 버튼 클릭 Then 옵션 선택 안내 메시지가 표시된다

### 바로 구매 — @ORDER/BUY_NOW

- Given 옵션 선택 완료 When 바로 구매 버튼 클릭 Then 선택된 옵션과 수량으로 주문서 작성 화면으로 이동한다
- Given 필수 옵션 미선택 When 바로 구매 버튼 클릭 Then 옵션 선택 안내 메시지가 표시된다

### 리뷰 조회 — @REVIEW/RATING_SUMMARY, @REVIEW/REVIEW_LIST

- Given 리뷰 탭 선택 When 탭 콘텐츠 로드 완료 Then 평균 평점, 별점 분포, 리뷰 목록이 표시된다
- Given 리뷰 목록에 추가 페이지 존재 When 더보기 버튼 클릭 Then 다음 페이지의 리뷰가 목록 하단에 추가된다
- Given 등록된 리뷰 없음 When 리뷰 탭 선택 Then 리뷰 없음 안내 메시지가 표시된다
- Given 리뷰 목록 표시 When 정렬 기준 변경 Then 선택한 기준으로 리뷰 목록이 재정렬된다

### 문의 조회 — @QNA/QNA_LIST

- Given 문의 탭 선택 When 탭 콘텐츠 로드 완료 Then 문의 목록이 표시된다
- Given 문의 목록에 항목 존재 When 문의 항목 클릭 Then 해당 문의의 본문과 답변이 펼쳐진다
- Given 등록된 문의 없음 When 문의 탭 선택 Then 문의 없음 안내 메시지가 표시된다

### 상세 설명 — @PRODUCT/DETAIL_DESCRIPTION

- Given 상세정보 탭 선택 When 탭 콘텐츠 로드 완료 Then 상품 상세 설명 콘텐츠가 표시된다

## UserStory

### 상품 정보 확인 — @PRODUCT/INFO

- 소비자로서 상품의 가격과 배송 정보를 한눈에 확인하고 싶다, 구매 여부를 판단하기 위해

### 이미지 탐색 — @PRODUCT/IMAGE_GALLERY

- 소비자로서 상품의 다양한 이미지를 탐색하고 싶다, 실물에 가까운 상품 외관을 확인하기 위해

### 옵션 조합 선택 — @PRODUCT/OPTION

- 소비자로서 원하는 옵션 조합을 선택하고 싶다, 정확한 상품 구성으로 구매하기 위해

### 수량 조정 — @PRODUCT/QUANTITY

- 소비자로서 원하는 수량을 지정하고 합계 금액을 확인하고 싶다, 결제 전 총 비용을 파악하기 위해

### 장바구니 담기 — @CART/ADD_TO_CART

- 소비자로서 선택한 상품을 장바구니에 담고 싶다, 다른 상품과 함께 비교하거나 나중에 구매하기 위해

### 바로 구매 — @ORDER/BUY_NOW

- 소비자로서 선택한 상품을 바로 구매하고 싶다, 빠르게 주문을 완료하기 위해

### 리뷰 조회 — @REVIEW/RATING_SUMMARY, @REVIEW/REVIEW_LIST

- 소비자로서 다른 구매자의 리뷰와 평점을 확인하고 싶다, 상품의 실제 품질과 만족도를 파악하기 위해

### 문의 조회 — @QNA/QNA_LIST

- 소비자로서 다른 사람의 문의와 답변을 확인하고 싶다, 궁금한 점이 이미 해결되었는지 확인하기 위해

### 상세 설명 — @PRODUCT/DETAIL_DESCRIPTION

- 소비자로서 상품의 상세한 설명을 확인하고 싶다, 사양과 특징을 충분히 이해한 뒤 구매를 결정하기 위해
