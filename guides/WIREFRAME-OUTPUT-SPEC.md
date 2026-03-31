# FlowFrame Wireframe Output Spec

> FlowFrame에 업로드 가능한 와이어프레임 HTML을 생성하기 위한 기술 스펙.
> 외부 스킬 프로젝트는 이 문서를 참조하여 산출물을 만든다.

---

## 1. 파일 제약

| 항목 | 조건 |
|------|------|
| 확장자 | `.html` |
| 최대 크기 | 2 MB |
| 인코딩 | UTF-8 |

---

## 2. 필수 메타데이터 블록

`<head>` 안에 아래 형태의 `<script>` 블록이 **반드시** 존재해야 한다.

```html
<script type="application/json" id="flowframe-meta">
{
  "generator": "flowframe-wireframe-skill",
  "version": "1.2",
  "screenId": "LOGIN",
  "title": "로그인",
  "purpose": "사용자가 이메일과 비밀번호로 서비스에 로그인하는 화면",
  "author": "jay",
  "viewport": "pc",
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
            {
              "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
              "type": "input",
              "label": "이메일",
              "description": "사용자가 로그인에 사용할 이메일 주소를 입력하고 형식 오류 여부를 확인하는 입력 필드"
            }
          ]
        }
      ]
    }
  ]
}
</script>
```

### 필드 정의

| 필드 | 필수 | 타입 | 설명 |
|------|:----:|------|------|
| `generator` | O | `string` | **고정값** `"flowframe-wireframe-skill"`. 이 값이 아니면 업로드 차단. |
| `version` | O | `string` | 스펙 버전. 현재 `"1.0"`. |
| `screenId` | O | `string` | 화면 식별자. FlowFrame의 screen slug와 매칭된다. |
| `title` | O | `string` | 화면 제목. |
| `purpose` | O | `string` | 화면 목적 설명. 와이어프레임 뷰어의 상단 설명으로 표시된다. |
| `author` | | `string` | 작성자. |
| `viewport` | | `"pc"` \| `"mobile"` | 뷰포트 유형. |
| `features` | O | `Feature[]` | 화면의 feature 트리. **1개 이상** 필수. 재귀 구조를 지원한다. |

### Feature 필드

| 필드 | 타입 | 설명 |
|------|------|------|
| `featureId` | `string` | feature 폴더 경로에서 파생한 ID. `docs/features/` 이후 경로를 UPPER_SNAKE_CASE로 변환하고 깊이는 `__`로 구분. |
| `label` | `string` | feature 표시명. |
| `spec` | `string` | feature `index.md`의 상대 경로. |
| `elements` | `Element[]` | 이 feature의 추적 요소 목록. `elements` 또는 `features` 중 최소 하나 필요. |
| `features` | `Feature[]` | 하위 feature 목록. 재귀 구조. `elements` 또는 `features` 중 최소 하나 필요. |

### Element 필드

| 필드 | 타입 | 설명 |
|------|------|------|
| `id` | `string` | 추적 요소 ID. `FEATURE_{FEATURE_ID}_{ELEMENT_ID}` 형식 필수. |
| `type` | `string` | 요소 유형. `input`, `button`, `link`, `image`, `text`, `select`, `checkbox`, `radio`, `table`, `list`, `dialog` 중 하나. |
| `label` | `string` | 요소 이름 (UI에 표시). |
| `description` | `string` | 요소 설명. 사람 검토용 기능 설명까지 포함해야 하며, 라벨 반복이 아니라 표시 정보·사용자 행동·기능 목적을 함께 드러내야 한다. |

---

## 3. 업로드 검증 규칙

아래 순서로 검증하며, 하나라도 실패하면 업로드가 차단된다.

1. 파일 확장자가 `.html`인가
2. 파일 크기가 2MB 이하인가
3. HTML 파싱이 가능한가
4. `<script id="flowframe-meta">` 블록이 존재하는가
5. JSON 파싱이 가능한가
6. `generator` 필드가 `"flowframe-wireframe-skill"`인가
7. 필수 필드(`version`, `screenId`, `title`, `purpose`, `features`)가 모두 있는가
8. `features`가 배열이고 1개 이상인가
9. 각 feature에 `elements` 또는 `features` 중 최소 하나가 있는가

추가로 경고(업로드는 허용):
- 메타데이터 `screenId`가 현재 화면의 slug와 일치하지 않으면 경고 표시

---

## 4. `data-feature-id` 와 `data-feature` 속성

와이어프레임은 2단계 DOM 추적을 사용한다.

- `data-feature-id` / `data-feature-label`: feature 컨테이너용
- `data-feature`: 개별 추적 요소용

HTML body의 요소에 `data-feature="{ELEMENT_ID}"`를 부여하면, FlowFrame 와이어프레임 뷰어에서:
- 디스크립션 패널의 요소 목록과 **양방향 하이라이트** 연동
- 패널에서 요소에 마우스를 올리면 iframe 내 해당 영역이 하이라이트
- iframe 내 해당 영역에 마우스를 올리면 패널의 요소 항목이 하이라이트

feature 래퍼의 `data-feature-id`를 클릭하면 해당 feature 범위에 코멘트를 남길 수 있다.

### `data-feature-id` / `data-feature-label`

- 모든 feature는 `data-feature-id="{featureId}"` 와 `data-feature-label="{label}"` 을 가진 래퍼를 받는다
- 하위 feature가 있으면 상위 `data-feature-id` 래퍼 안에 중첩된다
- feature에 `elements`와 하위 `features`가 동시에 있으면, `data-feature` 요소와 하위 래퍼가 같은 레벨에 공존할 수 있다
- 모달의 경우 `data-feature-id`는 트리거가 아니라 모달 카드 본문에 둔다

### 부여 대상

`data-feature` 는 **개별 추적 요소**(가장 작은 의미 단위의 UI 요소)에만 부여한다. 레이아웃용 wrapper div 에는 부여하지 않는다.

- `data-feature` 는 metadata `elements[].id` 와 매칭되어야 한다
- 하나의 feature 가 여러 추적 요소를 가지면, feature 래퍼 안의 개별 요소 각각에 `data-feature` 를 부여한다
- 여러 DOM 노드가 같은 `data-feature` 를 공유할 수 있는데, 이 경우 같은 추적 요소의 상호배타 상태(예: 리뷰 탭 패널)를 나타내는 경우에만 허용된다
- 기본 상태에서는 같은 `data-feature` 값을 가진 요소 중 최소 하나가 보여야 한다
- 고정 영역은 `data-feature` 를 부여하지 않는다
- 고정 영역은 메타데이터에도 포함하지 않는다

### 올바른 예시

feature 래퍼에는 `data-feature-id`가 있고, 개별 추적 요소에만 `data-feature`가 있다:

```html
<div data-feature-id="AUTH__LOGIN_FORM" data-feature-label="로그인 폼" class="flex flex-col gap-4">
  <div data-feature="FEATURE_AUTH__LOGIN_FORM_EMAIL" class="flex flex-col gap-1.5">
    <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">이메일</label>
    <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="user@example.com" readonly />
  </div>
  <div data-feature="FEATURE_AUTH__LOGIN_FORM_PASSWORD" class="flex flex-col gap-1.5">
    <label class="text-sm font-medium text-zinc-600 dark:text-zinc-400">비밀번호</label>
    <input class="h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent" type="text" value="********" readonly />
  </div>
  <button data-feature="FEATURE_AUTH__LOGIN_FORM_SUBMIT" class="h-10 w-full text-sm font-semibold bg-zinc-800 text-white dark:bg-zinc-200 dark:text-zinc-900 rounded-md">
    로그인
  </button>
</div>
```

### 잘못된 예시

feature 래퍼 없이 layout wrapper 에 직접 `data-feature` 를 부여하면 안 된다:

```html
<!-- WRONG: wrapper 에 data-feature 부여 -->
<div data-feature="FEATURE_AUTH__LOGIN_FORM">
  <input type="text" />
  <input type="password" />
  <button>로그인</button>
</div>
```

---

## 5. 제공 CSS/JS 에셋 및 Tailwind CSS v4

FlowFrame은 Tailwind CSS v4를 통해 와이어프레임 스타일링을 지원한다.

### 필수 에셋

```html
<!-- Tailwind CSS v4 (browser JIT — 업로드 시 제거, 뷰어가 재주입) -->
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>

<!-- 다크 모드 설정 (필수) -->
<style type="text/tailwindcss">
  @custom-variant dark (&:where(.dark, .dark *));
</style>
```

> **참고**: 업로드 시 모든 `<script>` 태그가 보안상 제거된다. FlowFrame 뷰어는 HTML을 srcdoc에 넣기 전에 Tailwind CDN 스크립트를 자동으로 재주입하며, iframe sandbox에 `allow-scripts`를 추가하여 Tailwind JIT 컴파일러가 실행될 수 있도록 한다.

### 스타일링 규칙

- 모든 스타일링은 **Tailwind CSS v4 유틸리티 클래스**를 사용한다
- 컬러 팔레트: **zinc**를 일관되게 사용 (그레이스케일 와이어프레임 미학)
- 모든 색상 클래스에 **`dark:` 변형**을 반드시 포함한다
- `wireframe-base.css`는 CSS 리셋과 폰트 기본값만 제공한다
- 레이아웃, 폼, 버튼, 타이포그래피 등은 모두 Tailwind 유틸리티로 처리한다

### 주요 Tailwind 패턴

| 요소 | Tailwind Classes |
|------|-----------------|
| 페이지 래퍼 | `min-h-screen flex items-center justify-center p-6 bg-zinc-50 dark:bg-zinc-900` |
| 카드 | `w-full max-w-md flex flex-col gap-6 p-8 bg-white dark:bg-zinc-800 border border-zinc-200 dark:border-zinc-700 rounded-lg shadow-sm` |
| 컨테이너 | `w-full max-w-5xl mx-auto p-6` |
| 필드 래퍼 | `flex flex-col gap-1.5` |
| 라벨 | `text-sm font-medium text-zinc-600 dark:text-zinc-400` |
| 텍스트 입력 | `h-10 px-3 text-sm border border-zinc-300 dark:border-zinc-600 rounded-md bg-transparent` |
| 주요 버튼 | `h-10 px-4 text-sm font-semibold bg-zinc-800 text-white dark:bg-zinc-200 dark:text-zinc-900 rounded-md` |
| 보조 버튼 | `h-10 px-4 text-sm font-semibold border border-zinc-300 dark:border-zinc-600 text-zinc-600 dark:text-zinc-400 rounded-md` |
| 링크 | `text-sm text-zinc-500 dark:text-zinc-400 hover:underline` |
| 플레이스홀더 | `bg-zinc-200 dark:bg-zinc-700 rounded` |

---

## 6. 다크 모드

FlowFrame은 사용자 테마에 따라 iframe의 `<html>` 요소에 `.dark` 클래스를 자동 토글한다.

Tailwind CSS v4의 기본 다크 모드는 `prefers-color-scheme` 기반이므로, 클래스 기반 다크 모드를 사용하려면 각 와이어프레임 HTML에 아래 설정이 **필수**이다:

```html
<style type="text/tailwindcss">
  @custom-variant dark (&:where(.dark, .dark *));
</style>
```

모든 색상 관련 클래스에 `dark:` 변형을 포함해야 한다:
- 배경: `bg-white dark:bg-zinc-800`
- 텍스트: `text-zinc-800 dark:text-zinc-200`
- 테두리: `border-zinc-200 dark:border-zinc-700`

---

## 7. ID 규칙

- `screenId`: 화면을 식별하는 고유 ID. 대문자 기반 식별자를 사용한다 (예: `LOGIN`, `DASHBOARD`, `ORDER_MANAGEMENT`).
- `featureId`: feature 폴더 경로에서 파생한 ID. `docs/features/` 이후 경로를 UPPER_SNAKE_CASE로 변환하고 깊이는 `__`(더블 언더스코어)로 구분한다 (예: `AUTH__LOGIN_FORM`, `AUTH__2FA__TOTP`, `CART_ITEMS`).
  - 단일 언더스코어(`_`)는 단어 연결용 (예: `CART_ITEMS`)
  - 더블 언더스코어(`__`)는 폴더 깊이 구분용 (예: `AUTH__LOGIN_FORM`)
- `element.id`: `FEATURE_{FEATURE_ID}_{ELEMENT_ID}` 형식 (예: `FEATURE_AUTH__LOGIN_FORM_EMAIL`, `FEATURE_AUTH__LOGIN_FORM_SUBMIT`).
- 각 `element.id` 는 HTML 안의 하나 이상의 DOM 요소에 `data-feature` 속성으로 매핑될 수 있다. 다만 중복 매핑은 같은 추적 요소의 상호배타 상태 표현일 때만 허용한다.
- 같은 화면 안에서 각 `element.id`는 유일해야 한다.

### `ELEMENT_ID` 도출 규칙

- 요소명에서 의미가 드러나는 핵심 영문 명사를 선택한다.
- 영문 대문자만 사용하고, 여러 단어면 언더스코어로 연결한다.
- `BUTTON`, `TEXT`, `INPUT`처럼 요소 타입만 반복하는 일반어는 피하고 역할 중심으로 이름 짓는다.
- 같은 feature 안에서 중복될 수 있으면 역할이나 위치를 더 붙여 구분한다.

| 요소명 | 권장 `ELEMENT_ID` | 비고 |
|------|-------------------|------|
| 이메일 | `EMAIL` | 입력 타입이 아니라 도메인 의미 사용 |
| 비밀번호 | `PASSWORD` | |
| 로그인 버튼 | `SUBMIT` | 버튼 타입 반복 대신 액션 의미 사용 |
| 등록 버튼 | `SUBMIT` | feature 맥락에서 제출 동작이면 `SUBMIT` |
| 저장 버튼 | `SAVE` | |
| 삭제 버튼 | `DELETE` | |
| 댓글 목록 | `LIST` | feature가 `COMMENTS`이면 `FEATURE_COMMENTS_LIST` |
| 댓글 입력 | `INPUT` | 같은 feature에서 목록과 입력을 구분할 때 허용 |
| 서비스 로고 | `LOGO` | |
| 소셜 로그인 | `SOCIAL` | 버튼 그룹 전체를 하나로 추적할 때 |

모호하면 요소 타입보다 사용자 행동이나 정보 의미를 우선한다.

---

## 8. PC / Mobile 분리

PC와 Mobile은 **별도 HTML 파일**로 생성한다. 하나의 HTML 안에서 CSS 미디어 쿼리로 분리하지 않는다.

- 단일 viewport 화면: `docs/screens/{SCREEN}/wireframe.html`
- PC 파일: `docs/screens/{SCREEN}/wireframe-pc.html`, `viewport` 필드를 `"pc"`로 설정
- Mobile 파일: `docs/screens/{SCREEN}/wireframe-mobile.html`, `viewport` 필드를 `"mobile"`로 설정

FlowFrame에서 각각 별도 업로드하고, 뷰포트 토글로 전환하여 확인한다.

---

## 9. 전체 예시 (PC)

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
    "author": "작성자",
    "viewport": "pc",
    "purpose": "사용자가 이메일과 비밀번호로 서비스에 로그인하는 화면",
    "elements": [
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "input",
        "label": "이메일",
        "description": "사용자 이메일 주소를 입력하는 텍스트 필드",
        "spec": "../../features/auth/login-form/index.md"
      },
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_PASSWORD",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "input",
        "label": "비밀번호",
        "description": "비밀번호를 입력하는 필드",
        "spec": "../../features/auth/login-form/index.md"
      },
      {
        "id": "FEATURE_AUTH__LOGIN_FORM_SUBMIT",
        "featureId": "AUTH__LOGIN_FORM",
        "type": "button",
        "label": "로그인",
        "description": "인증 요청을 보내는 주요 액션 버튼",
        "spec": "../../features/auth/login-form/index.md"
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
