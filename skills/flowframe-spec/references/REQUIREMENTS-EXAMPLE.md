# Example: Requirements Definition — Product Detail

This example shows what `docs/screens/PRODUCT_DETAIL/requirements.md` should contain.
This file is the **canonical source for screen-level user stories and acceptance criteria**.

## Example — `docs/screens/PRODUCT_DETAIL/requirements.md`

```markdown
# 상품 상세 Requirements

## 바로가기

- [상품 요약](#상품-요약)
- [옵션 선택](#옵션-선택)
- [리뷰 확인](#리뷰-확인)

## 유저스토리

### 상품 요약

- 사용자로서 상품명, 가격, 대표 이미지를 빠르게 확인하고 싶다, 이 상품이 내가 찾는 상품인지 즉시 판단하기 위해

### 옵션 선택

- 사용자로서 원하는 옵션과 수량을 선택하고 싶다, 구매 가능한 조합으로 바로 주문을 진행하기 위해
- 사용자로서 품절 여부를 미리 알고 싶다, 주문 불가능한 옵션을 선택하지 않기 위해

### 리뷰 확인

- 사용자로서 다른 사용자의 후기를 보고 싶다, 구매 판단의 근거를 얻기 위해

## 인수조건

### 상품 요약

- Given 상품 상세 화면에 진입하면 When 상단 요약 영역을 확인하면 Then 상품명, 가격, 대표 이미지가 표시된다

### 옵션 선택

- Given 필수 옵션을 모두 선택하지 않은 상태일 때 When 화면을 확인하면 Then 구매 버튼과 장바구니 버튼은 비활성화된다
- Given 필수 옵션을 모두 선택한 상태일 때 When 구매 가능 재고가 있으면 Then 구매 버튼과 장바구니 버튼이 활성화된다
- Given 품절 옵션을 선택한 상태일 때 When 화면을 확인하면 Then 품절 안내가 표시되고 구매 관련 버튼이 비활성화된다

### 리뷰 확인

- Given 리뷰가 존재하지 않는 상품일 때 When 리뷰 영역을 확인하면 Then 빈 상태 안내가 표시된다
```

## Key patterns

- `requirements.md` is a structured document, not a scratchpad.
- Free-form planner notes belong in `notes.md`, not here.
- `## 바로가기` helps the agent open only the needed section.
- Use stable `### responsibility group` headings that do not change across updates.
- Use the heading text itself as the anchor source; do not define a separate manual slug.
- Keep related user stories and acceptance criteria together under one responsibility group.
- Do not place Test Points, E2E checklists, or implementation notes here.
