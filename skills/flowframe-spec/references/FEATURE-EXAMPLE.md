# Example: Feature Spec — 상품 상세

> **featureId는 경로에서 파생된다.** frontmatter에 featureId를 쓰지 않는다.
> 예: `docs/features/product-detail/summary/index.md` → featureId = `PRODUCT_DETAIL__SUMMARY`
> 예: `docs/features/product-detail/options/index.md` → featureId = `PRODUCT_DETAIL__OPTIONS`

## Leaf 예제 — `docs/features/product-detail/options/index.md`

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

## 유저스토리

- 사용자로서 원하는 색상과 사이즈를 선택하고 싶다, 내게 맞는 상품을 구매하기 위해
- 사용자로서 구매 전에 품절 여부를 알고 싶다, 주문 불가 옵션을 미리 피하기 위해
- 사용자로서 선택한 옵션을 장바구니에 담고 싶다, 다른 상품과 함께 비교 후 구매하기 위해

## 인수조건

- Given 필수 옵션을 모두 선택하면 Then 구매 버튼과 장바구니 버튼이 활성화된다
- Given 품절 옵션을 선택하면 Then 재고 안내에 품절 상태가 표시되고 구매 관련 버튼이 비활성화된다
- Given 허용 재고보다 큰 수량을 입력하면 Then 수량이 반영되지 않거나 경고가 표시된다
- Given 장바구니 버튼을 클릭하면 Then 현재 선택 옵션과 수량이 장바구니에 추가된다

## 비즈니스 로직

- 필수 옵션을 모두 선택해야 구매 가능
- 품절 옵션은 선택 가능하더라도 구매/장바구니 실행 불가
- 최소 수량 1개, 최대 수량은 선택 옵션 재고 수량 이하
- 옵션 조합이 바뀌면 가격과 재고 정보 즉시 재계산
```

## Branch 예제 — `docs/features/product-detail/index.md`

branch는 자식 폴더가 있는 피쳐다. `## 와이어프레임 요소`와 `usedIn`이 없으며, 하위 leaf들의 공통 도메인 컨텍스트만 담는다. 와이어프레임 스킬은 branch를 읽지 않는다.

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

- **Leaf는 구현 단위다.** 옵션 선택처럼 독립적으로 설명되고 구현 가능한 책임은 leaf `index.md`에 작성한다.
- **Branch는 공통 컨텍스트다.** `product-detail/index.md`는 하위 leaf들이 공유하는 상태, 규칙, 인터랙션만 담고 직접 렌더링하지 않는다.
- **유저스토리와 인수조건은 leaf에 작성한다.** 기능 자체의 검증 기준은 `docs/features/product-detail/options/index.md` 같은 leaf에 둔다.
- **같은 branch 아래 leaf들이 함께 화면을 구성할 수 있다.** screen spec은 `summary`, `options`, `reviews` 같은 여러 leaf를 조합해 하나의 화면을 만든다.
