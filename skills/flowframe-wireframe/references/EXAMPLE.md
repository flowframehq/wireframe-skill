# 예제: 단일 feature 화면 와이어프레임

단순한 **단일 feature 화면**의 흐름을 보여주는 레퍼런스: 기능 명세 + 화면 명세 → 생성된 와이어프레임 HTML.
재사용 가능한 리뷰 탭 래퍼 자체는 [REVIEW-TABS.md](REVIEW-TABS.md)를 참조한다.

## 목차

- [1단계: 기능 명세](#1단계-기능-명세)
- [2단계: 화면 명세](#2단계-화면-명세)
- [3단계: 생성된 와이어프레임](#3단계-생성된-와이어프레임)
- [핵심 패턴](#핵심-패턴)

## 1단계: 기능 명세

> **참고:** `featureId`는 frontmatter에 쓰지 않는다. 폴더 경로에서 자동 파생된다.
> 예: `docs/features/auth/login-form/index.md` → `AUTH__LOGIN_FORM`
>
> `auth`는 하위 feature가 있는 폴더이며, 이 예제에서는 `elements`가 없는 순수 그루핑 feature다.
> 화면이 참조하는 대상은 `elements`가 있는 feature다.

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

## 비즈니스 로직

- 이메일 형식 검증
- 5회 실패 시 30분 잠금
- 비밀번호 최소 8자, 영문+숫자

requirements: ../../screens/LOGIN/requirements.md#인증
```

## 2단계: 화면 명세

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

## 2.5단계: 화면 요구사항

```markdown
# 로그인 Requirements

## 유저스토리

### 인증
- 사용자로서 이메일과 비밀번호로 로그인하고 싶다, 내 계정에 접근하기 위해
- 사용자로서 로그인 실패 시 원인을 알고 싶다, 올바른 정보를 다시 입력하기 위해

## 인수조건

### 인증
- Given 이메일과 비밀번호 입력 완료 When 로그인 버튼 클릭 Then 인증 요청 후 메인 화면 이동
- Given 이메일 형식 오류 When 로그인 버튼 클릭 Then 이메일 형식 에러 메시지 표시
- Given 5회 연속 로그인 실패 When 6번째 시도 Then 계정 잠금 안내 메시지 표시
```

## 3단계: 생성된 와이어프레임

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

    .feature-review:has(> input:nth-of-type(1):checked) .review-tabs label:nth-of-type(1),
    .feature-review:has(> input:nth-of-type(2):checked) .review-tabs label:nth-of-type(2),
    .feature-review:has(> input:nth-of-type(3):checked) .review-tabs label:nth-of-type(3),
    .feature-review:has(> input:nth-of-type(4):checked) .review-tabs label:nth-of-type(4) {
      @apply bg-white text-zinc-900 font-semibold shadow-sm dark:bg-zinc-800 dark:text-zinc-100;
    }
  </style>

  <script type="application/json" id="flowframe-meta">
  {
    "generator": "flowframe-wireframe-skill",
    "version": "1.2",
    "screenId": "LOGIN",
    "title": "로그인",
    "viewport": "pc",
    "purpose": "사용자가 이메일과 비밀번호로 서비스에 로그인하는 화면",
    "features": [
      {
        "featureId": "AUTH",
        "label": "인증",
        "spec": "../../features/auth/index.md",
        "features": [
          {
            "featureId": "AUTH__LOGIN_FORM",
            "label": "로그인 폼",
            "spec": "../../features/auth/login-form/index.md",
            "elements": [
              { "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL", "type": "input", "label": "이메일", "description": "사용자가 로그인에 사용할 이메일 주소를 입력하고 형식 오류 여부를 확인하는 입력 필드" },
              { "id": "FEATURE_AUTH__LOGIN_FORM_PASSWORD", "type": "input", "label": "비밀번호", "description": "사용자 계정 인증에 필요한 비밀번호를 마스킹된 상태로 입력하는 보안 입력 필드" },
              { "id": "FEATURE_AUTH__LOGIN_FORM_SUBMIT", "type": "button", "label": "로그인 버튼", "description": "입력한 이메일과 비밀번호로 인증을 요청하고 다음 화면 이동 흐름을 시작하는 주요 실행 버튼" }
            ]
          }
        ]
      }
    ]
  }
  </script>
</head>
<body class="bg-zinc-50 dark:bg-zinc-900 text-zinc-800 dark:text-zinc-200">
  <div class="flex min-h-screen min-w-[1200px] items-center justify-center p-6">
    <div data-feature-id="AUTH" data-feature-label="인증">
      <div data-feature-id="AUTH__LOGIN_FORM" data-feature-label="로그인 폼" class="feature-review group/login relative w-full max-w-md flex flex-col gap-6 rounded-lg border border-zinc-200 bg-white p-8 shadow-sm dark:border-zinc-700 dark:bg-zinc-800">
        <input class="peer/default sr-only" type="radio" name="login-review" id="login-default" checked />
        <input class="peer/error sr-only" type="radio" name="login-review" id="login-error" />

        <div class="review-tabs absolute -top-3 right-4 z-20 flex items-center gap-1 px-1.5 py-1 rounded-full border border-dashed border-zinc-300 dark:border-zinc-600 bg-zinc-50/95 dark:bg-zinc-900/95 shadow-sm opacity-0 pointer-events-none translate-y-1 transition-all group-hover/login:opacity-100 group-hover/login:pointer-events-auto group-hover/login:translate-y-0 group-focus-within/login:opacity-100 group-focus-within/login:pointer-events-auto group-focus-within/login:translate-y-0">
          <label for="login-default" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">기본</label>
          <label for="login-error" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">에러</label>
        </div>

        <div class="hidden peer-checked/default:block">
          <h1 class="text-lg font-semibold text-center">로그인</h1>

          <div class="flex flex-col gap-4 mt-6">
            <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_EMAIL">
              <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">이메일</label>
              <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="user@example.com" readonly />
            </div>
            <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_PASSWORD">
              <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">비밀번호</label>
              <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="********" readonly />
            </div>
          </div>

          <button class="h-10 w-full mt-6 text-sm font-semibold bg-zinc-800 text-white dark:bg-zinc-200 dark:text-zinc-900 rounded-md" data-feature="FEATURE_AUTH__LOGIN_FORM_SUBMIT">
            로그인
          </button>
        </div>

        <div class="hidden peer-checked/error:block">
          <h1 class="text-lg font-semibold text-center">로그인</h1>

          <div class="flex flex-col gap-4 mt-6">
            <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_EMAIL">
              <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">이메일</label>
              <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="user@example.com" readonly />
            </div>
            <div class="flex flex-col gap-1.5" data-feature="FEATURE_AUTH__LOGIN_FORM_PASSWORD">
              <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">비밀번호</label>
              <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="********" readonly />
              <div class="text-xs text-zinc-500 dark:text-zinc-400">이메일 또는 비밀번호가 올바르지 않습니다</div>
            </div>
          </div>

          <button class="h-10 w-full mt-6 text-sm font-semibold bg-zinc-800 text-white dark:bg-zinc-200 dark:text-zinc-900 rounded-md" data-feature="FEATURE_AUTH__LOGIN_FORM_SUBMIT">
            로그인
          </button>
        </div>
      </div>
    </div>
  </div>
</body>
</html>
```

## 핵심 패턴

1. **재귀 메타데이터 + DOM 중첩**: 메타데이터에서 `AUTH`가 `AUTH__LOGIN_FORM`을 하위 `features`로 포함하고, DOM에서도 `data-feature-id="AUTH"` 래퍼가 `data-feature-id="AUTH__LOGIN_FORM"` 래퍼를 감싼다.
2. **그루핑 feature (이 예제에서)**: `AUTH`는 `elements`가 없고 `features`만 있는 순수 그루핑 feature다. 자체 UI 요소가 필요하면 `elements`를 추가할 수 있다.
3. **featureId는 경로 파생**: frontmatter에 `featureId`가 없다. `auth/login-form/index.md` 경로가 자동으로 `AUTH__LOGIN_FORM`이 된다 (단일 `_`는 단어 연결, 이중 `__`는 깊이 구분).
4. **화면은 `elements`가 있는 feature를 참조**: 레이아웃에서 `[@auth/login-form](../../features/auth/login-form/index.md)` — `elements`가 있는 feature를 가리킨다.
5. **요구사항은 화면 수준에 배치**: feature에 `## 유저스토리`나 `## 인수조건`이 없다. 대신 `requirements:` 포인터로 `docs/screens/LOGIN/requirements.md#인증`을 가리킨다.
6. **ID 생성**: `FEATURE_{featureId}_{ELEMENT_ID}` — 예: `FEATURE_AUTH__LOGIN_FORM_EMAIL`. `featureId` 부분에 `__` 깊이 구분자가 유지된다.
7. **spec은 feature index.md를 가리킴**: 모든 요소가 `"spec": "../../features/auth/login-form/index.md"` — `docs/screens/LOGIN/`에서 feature `index.md`까지의 상대 경로.
8. **description은 리뷰어용**: 메타데이터 `description`은 단순 라벨이 아니라 로그인 흐름에서 각 컨트롤의 역할을 설명한다.
9. **리뷰 탭은 제품 UI 밖에 배치**: 에러 상태는 작은 hover/focus 오버레이로 전환되어 와이어프레임 도구로 인식되지, 실제 화면 내비게이션이 아니다.
10. **활성 탭은 `:has()` CSS 사용**: 패널 전환은 `peer-checked` (패널이 input의 형제). 라벨 활성 스타일은 `<style>`의 공유 `:has()` 규칙으로 처리. 라벨에 `peer-checked` 클래스 없음. group 이름 `group/login`으로 hover 드러남을 feature별 스코핑.
11. **상태 패널에서 추적 ID 재사용 가능**: 같은 `data-feature`가 상호배타 상태 패널에서만 반복 가능 — 기본 상태에서 최소 하나가 보여야 함.
12. **PC 기준선 유지**: 중앙 정렬 단일 포커스 화면에서도 `min-w-[1200px]` 외곽 셸이 PC 레이아웃 규칙을 충족.
13. **2-tier DOM 추적**: feature 래퍼에 `data-feature-id="AUTH__LOGIN_FORM"` (코멘트 앵커)과 `data-feature-label="로그인 폼"` (표시명). 내부 요소에 `data-feature`로 호버 하이라이트. FlowFrame 클릭은 컨테이너를 대상, 하이라이트는 개별 요소를 대상.
