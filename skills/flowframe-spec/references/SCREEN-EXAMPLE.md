# Example: Screen Spec — 상품 상세

```markdown
---
screenId: PRODUCT_DETAIL
title: 상품 상세
purpose: 사용자가 상품 정보를 확인하고 원하는 옵션으로 구매를 결정하는 화면
viewport: pc
---

# 상품 상세

## 레이아웃

1. 상단 헤더 — 로고, 검색창, 장바구니, 프로필
2. 상단 요약 영역 — [@product-detail/summary](../../features/product-detail/summary/index.md)
3. 옵션 선택 영역 — [@product-detail/options](../../features/product-detail/options/index.md)
4. 리뷰 영역 — [@product-detail/reviews](../../features/product-detail/reviews/index.md)

## 화면 유저스토리

- 사용자로서 상품 정보와 리뷰를 함께 확인하고 싶다, 구매 판단에 필요한 근거를 한 화면에서 보기 위해
- 사용자로서 원하는 옵션과 수량을 선택한 뒤 바로 구매하거나 장바구니에 담고 싶다, 구매 흐름을 빠르게 진행하기 위해

<!-- 기능 자체의 유저스토리와 인수조건은 각 feature leaf에 작성한다.
여기에는 화면 수준 목표와 feature 간 연동 흐름을 중심으로 작성한다. -->

## 화면 인수조건

- Given 사용자가 옵션을 변경하면 Then 상단 요약과 옵션 선택 영역에 가격, 재고, 구매 가능 상태가 일관되게 반영된다
- Given 품절 옵션을 선택하면 Then 구매 관련 CTA가 비활성화되고 품절 안내가 화면에 표시된다
- Given 리뷰가 없으면 Then 리뷰 영역에 빈 상태 안내가 표시되고 상품 구매 흐름은 계속 진행할 수 있다
```

## Key patterns

- Screen spec is the **planning document** — layout + screen-level user goal + screen-level acceptance criteria
- No feature implementation details here (those live in `docs/features/{name}/index.md`). Screen is also a folder (`docs/screens/{NAME}/index.md`), so relative paths to features use `../../features/`.
- `[@featureName](path)` syntax links to feature specs — path always ends with `/index.md`
- **Screen은 leaf만 참조한다.** branch(자식 폴더가 있는 피쳐)를 직접 가리키면 안 된다. 항상 leaf(자식 폴더가 없는 피쳐)의 `index.md`를 참조해야 한다.
- Every screen must reference at least one feature
- One feature can appear in multiple screens
- **Fixed areas** (line 1): Areas without `[@feature]` links describe fixed UI chrome such as headers. These render as plain HTML without `data-feature` and are not included in FlowFrame metadata.
- **Branch children in one screen** (lines 2, 3, 4): The screen references multiple leaf specs under the same branch `product-detail/*`, showing how one domain branch can be decomposed into separate renderable units.
- **Feature vs screen stories**: 기능 자체의 유저스토리와 인수조건은 feature leaf에 작성한다. Screen spec에는 화면 수준 목표와 feature 간 연동 흐름을 중심으로 짧게 작성한다.
- **Screen-level AC**: Screen spec의 인수조건은 개별 기능 구현 규칙이 아니라, 화면에서 사용자가 작업을 완료하는 흐름과 feature 간 연결 결과를 검증한다.

## Single-feature screen example

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 서비스에 로그인하는 화면
viewport: [pc, mobile]
---

# 로그인

## 레이아웃 (PC)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 레이아웃 (Mobile)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 화면 유저스토리

- 사용자로서 현재 기기 화면에 맞는 로그인 UI로 빠르게 인증하고 싶다, 서비스에 진입하기 위해

## 화면 인수조건

- Given PC 또는 모바일 화면으로 진입하면 Then 해당 viewport에 맞는 로그인 레이아웃이 표시된다
```

If the screen is effectively one feature, keep the screen spec this short instead of splitting it into micro-regions.
