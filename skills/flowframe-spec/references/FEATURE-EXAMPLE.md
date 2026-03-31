# Example: Feature Definition — Product Detail

> **`featureId` is path-derived.** Do not write `featureId` in frontmatter.
> Example: `docs/features/product-detail/summary/index.md` → `PRODUCT_DETAIL__SUMMARY`
> Example: `docs/features/product-detail/options/index.md` → `PRODUCT_DETAIL__OPTIONS`

## Leaf Example — `docs/features/product-detail/options/index.md`

```markdown
---
label: 옵션 선택
type: section
usedIn:
  - docs/screens/PRODUCT_DETAIL/index.md
---

# 옵션 선택

## 와이어프레임 요소

| 요소 | type | 설명 |
|------|------|------|
| 옵션 그룹 | list | 색상, 사이즈 등 구매 가능한 옵션 조합 표시 |
| 수량 선택 | input | 구매 수량 입력 또는 증감 제어 |
| 구매 버튼 | button | 선택한 옵션으로 바로 구매 진행 |
| 장바구니 버튼 | button | 현재 선택 상태를 장바구니에 담기 |
| 재고 안내 | text | 선택 옵션의 재고 여부 또는 품절 상태 표시 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 대표 옵션이 선택되어 있고 구매 가능 상태 |
| 옵션 미선택 | 필수 옵션 선택 전 구매 버튼 비활성화 |
| 품절 | 선택한 옵션이 품절되어 구매/장바구니 버튼 비활성화 |
| 재고 부족 | 선택 수량이 재고를 초과해 경고 메시지 표시 |

## 인터랙션

- 옵션 변경 → 가격/재고/구매 가능 상태 즉시 갱신
- 수량 증가 또는 직접 입력 → 총 수량 검증 후 허용 범위 내에서 반영
- 구매 버튼 클릭 → 주문/결제 흐름으로 이동
- 장바구니 버튼 클릭 → 선택 옵션과 수량으로 장바구니 추가

## 비즈니스 로직

- 필수 옵션을 모두 선택해야 구매 가능
- 품절 옵션은 선택 가능하더라도 구매/장바구니 실행 불가
- 최소 수량 1개, 최대 수량은 선택 옵션 재고 수량 이하
- 옵션 조합이 바뀌면 가격과 재고 정보 즉시 재계산

requirements: ../../screens/PRODUCT_DETAIL/requirements.md#옵션-선택
```

## Branch Example — `docs/features/product-detail/index.md`

A branch is a feature that has child folders. It does not contain `## 와이어프레임 요소` or `usedIn`; it only holds shared domain context for child leaves. The wireframe skill does not read branches.

```markdown
---
label: 상품 상세
---

# 상품 상세

## 공통 상태

| 상태 | 설명 |
|------|------|
| 판매중 | 정상 구매 가능 |
| 일시 품절 | 일부 옵션 또는 전체 상품 구매 불가 |
| 판매 종료 | 상품 노출은 되지만 구매 불가 |

## 공통 인터랙션

- 옵션 변경 시 → 가격, 재고, CTA 상태 즉시 동기화
- 리뷰 영역 진입 시 → 최신 리뷰 목록 또는 빈 상태 표시

## 공통 비즈니스 로직

- 상품 상세 화면의 모든 leaf는 동일한 상품 ID를 기준으로 동작
- 판매 종료 상품은 구매 CTA를 노출하지 않거나 비활성화 상태로 표시
- 품절 여부와 가격 정보는 옵션 조합 기준으로 계산
```

## Key patterns

- **Leafs are implementation units.** Independently describable and buildable responsibilities should be written in a leaf `index.md`.
- **Branches are shared context.** `product-detail/index.md` contains only shared states, rules, and interactions for its child leaves and is not rendered directly.
- **Do not place user stories or acceptance criteria in features.** Verification belongs in the owning screen's `requirements.md`.
- **Add a required `requirements` pointer.** The one-line `requirements:` entry at the end of a leaf must point to the matching auto-generated heading anchor in `requirements.md`.
- **Multiple leaves under one branch can compose a screen together.** A screen spec may combine `summary`, `options`, and `reviews` into one page.
