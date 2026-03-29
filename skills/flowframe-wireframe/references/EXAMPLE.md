# Example: End-to-End Wireframe Generation

This example shows the full flow: feature spec + screen spec → wireframe HTML.

## Table of Contents

- [Step 1](#step-1)
- [Step 2](#step-2)
- [Step 3](#step-3)
- [Key Patterns](#key-patterns)

## Step 1

> **Note:** `featureId`는 frontmatter에 쓰지 않는다. 폴더 경로에서 자동 파생된다.
> 예: `docs/features/auth/login-form/index.md` → `AUTH__LOGIN_FORM`
>
> `auth`는 branch(자식 폴더 있음)이므로 `## 와이어프레임 요소`를 가질 수 없다.
> screen이 참조하는 대상은 항상 leaf다.

```markdown
---
label: 로그인 폼
type: section
usedIn:
  - docs/screens/LOGIN/index.md
---

# 로그인 폼

## 와이어프레임 요소

| 요소 | type | 설명 |
|------|------|------|
| 이메일 | input | 이메일 주소 입력 필드 |
| 비밀번호 | input | 비밀번호 입력 필드 (마스킹) |
| 로그인 버튼 | button | 인증 요청 주요 액션 버튼 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 폼 비어있음, 로그인 버튼 활성 |
| 에러 | "이메일 또는 비밀번호가 올바르지 않습니다" 메시지 |
| 로딩 | 로그인 버튼 비활성 + 스피너 |

## 인터랙션

- 로그인 버튼 클릭 → 인증 API 호출 → 성공 시 대시보드로 이동

## 유저스토리

- 사용자로서 이메일과 비밀번호로 로그인하고 싶다, 내 계정에 접근하기 위해
- 사용자로서 로그인 실패 시 원인을 알고 싶다, 올바른 정보를 다시 입력하기 위해

## 인수조건

- Given 이메일과 비밀번호 입력 완료 When 로그인 버튼 클릭 Then 인증 요청 후 메인 화면 이동
- Given 이메일 형식 오류 When 로그인 버튼 클릭 Then 이메일 형식 에러 메시지 표시
- Given 5회 연속 로그인 실패 When 6번째 시도 Then 계정 잠금 안내 메시지 표시

## 비즈니스 로직

- 이메일 형식 검증
- 5회 실패 시 30분 잠금
- 비밀번호 최소 8자, 영문+숫자
```

## Step 2

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 이메일과 비밀번호로 서비스에 로그인하는 화면
viewport: pc
---

# 로그인

## 레이아웃

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)
```

## Step 3

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>로그인</title>
  <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>

  <style type="text/tailwindcss">
    @custom-variant dark (&:where(.dark, .dark *));
  </style>

  <script type="application/json" id="flowframe-meta">
  {
    "generator": "flowframe-wireframe-skill",
    "version": "1.0",
    "screenId": "LOGIN",
    "title": "로그인",
    "viewport": "pc",
    "purpose": "사용자가 이메일과 비밀번호로 서비스에 로그인하는 화면",
    "elements": [
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "input",
        "label": "이메일",
        "description": "이메일 주소 입력 필드",
        "spec": "../features/auth/login-form/index.md"
      },
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_PASSWORD",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "input",
        "label": "비밀번호",
        "description": "비밀번호 입력 필드 (마스킹)",
        "spec": "../features/auth/login-form/index.md"
      },
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_SUBMIT",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "button",
        "label": "로그인 버튼",
        "description": "인증 요청 주요 액션 버튼",
        "spec": "../features/auth/login-form/index.md"
      }
    ]
  }
  </script>
</head>
<body class="bg-zinc-50 dark:bg-zinc-900 text-zinc-800 dark:text-zinc-200">
  <div class="min-h-screen flex items-center justify-center p-6">
    <div class="w-full max-w-md flex flex-col gap-6 p-8 bg-white dark:bg-zinc-800 border border-zinc-200 dark:border-zinc-700 rounded-lg shadow-sm">

      <h1 class="text-lg font-semibold text-center">로그인</h1>

      <div class="flex flex-col gap-4">
        <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_EMAIL">
          <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">이메일</label>
          <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="user@example.com" readonly />
        </div>
        <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_PASSWORD">
          <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">비밀번호</label>
          <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="********" readonly />
        </div>
      </div>

      <button class="h-10 w-full text-sm font-semibold bg-zinc-800 text-white dark:bg-zinc-200 dark:text-zinc-900 rounded-md" data-feature="FEATURE_AUTH__LOGIN_FORM_SUBMIT">
        로그인
      </button>

    </div>
  </div>
</body>
</html>
```

## Key Patterns

1. **Leaf, not branch**: The feature spec is at `docs/features/auth/login-form/index.md` (a leaf — no child folders), not `auth/index.md` (a branch). Branches never have `## 와이어프레임 요소` or `usedIn`.
2. **featureId is path-derived**: No `featureId` in frontmatter. The path `auth/login-form/index.md` automatically becomes `AUTH__LOGIN_FORM` (single `_` joins words, double `__` joins depth levels).
3. **Screen references leaf only**: The layout uses `[@auth/login-form](../../features/auth/login-form/index.md)` — always pointing to a leaf, never a branch like `@auth`.
4. **ID generation**: `FEATURE_{featureId}_{ELEMENT_ID}` — e.g., `FEATURE_AUTH__LOGIN_FORM_EMAIL`. The `featureId` portion preserves the `__` depth separator.
5. **spec points to leaf index.md**: All elements share `"spec": "../features/auth/login-form/index.md"` — the relative path from `docs/wireframes/` to the leaf's `index.md`.
6. **All styling uses Tailwind CSS v4 utility classes — no custom CSS classes or inline `<style>` blocks needed**
