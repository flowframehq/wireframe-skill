# Example: Screen Definition — Product Detail

This example shows what `docs/screens/PRODUCT_DETAIL/index.md` should contain.
The screen definition explains **layout and feature references only**, while planner scratch notes live in `notes.md` and user stories / acceptance criteria live in `requirements.md`.

## Example — `docs/screens/PRODUCT_DETAIL/index.md`

```markdown
---
screenId: PRODUCT_DETAIL
title: 상품 상세
purpose: 사용자가 상품 정보를 확인하고 옵션을 선택해 구매 결정을 내리는 화면
viewport: pc
---

# 상품 상세

## 레이아웃

1. 상단 헤더 — 로고, 검색창, 장바구니, 프로필
2. 상단 요약 영역 — [@product-detail/summary](../../features/product-detail/summary/index.md)
3. 옵션 선택 영역 — [@product-detail/options](../../features/product-detail/options/index.md)
4. 리뷰 영역 — [@product-detail/reviews](../../features/product-detail/reviews/index.md)

## 메모

- 초기 자유 메모는 `./notes.md`에 적는다
- 상세 유저스토리와 인수조건은 `./requirements.md`를 참조한다
```

## Key patterns

- Screen definition is the **layout contract** for the page.
- `index.md` should contain only layout and feature references.
- `[@feature](path)` must always point to a leaf feature `index.md`.
- Fixed chrome should be described as plain text, not turned into features.
- Free-form planner notes live in the sibling `notes.md`.
- Verification rules live in the sibling `requirements.md`.

## Single-feature screen example

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 이메일과 비밀번호로 서비스에 진입하는 화면
viewport: [pc, mobile]
---

# 로그인

## 레이아웃 (PC)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 레이아웃 (Mobile)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 메모

- 초기 자유 메모는 `./notes.md`에 적는다
- 유저스토리와 인수조건은 `./requirements.md` 참조
```
