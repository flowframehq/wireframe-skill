---
name: flowframe-spec
description: FlowFrame 프로젝트의 feature 명세(docs/features/*/index.md)와 screen 명세(docs/screens/*/index.md)를 작성하고 관리하도록 돕는다. 와이어프레임 요소, 상태, 인터랙션, 비즈니스 로직이 포함된 구조화 명세를 생성한다. "기획서", "기능명세", "화면 명세", "기능 추가", "화면 추가", "기획 도와줘", "명세", "기능 명세", "화면 명세", "기능 정의", "새 화면", "명세 삭제", "기능 삭제", "화면 삭제", "정합성 검증", "정합성 확인"처럼 feature나 screen을 생성·수정·삭제·기획하려는 요청에 사용한다. 사용자가 만들고 싶은 기능을 설명하거나, 명세 템플릿을 묻거나, screen에 feature를 추가/제거하거나, 명세 정합성을 확인하려 할 때도 사용한다.
license: MIT
metadata:
  author: flowframehq
  version: "2.0.0"
---

# FlowFrame 명세 작성기

FlowFrame 프로젝트에서 구조화된 명세를 작성하고 관리하도록 돕는다.
이 산출물은 `flowframe-wireframe` 스킬이 HTML 와이어프레임을 생성할 때 사용한다.

## 프로젝트 구조

```
project/docs/
├── features/           ← 기능 명세 (재귀 폴더 구조)
│   ├── auth/
│   │   ├── index.md        ← 상위 feature (공통 컨텍스트 + 자체 기능 가능)
│   │   ├── login-form/index.md   ← 하위 feature
│   │   └── social-login/index.md ← 하위 feature
│   ├── comments/index.md  ← 단일 feature
│   └── file-upload/index.md
├── screens/            ← 화면 명세 (화면별 폴더)
│   ├── LOGIN/index.md
│   ├── DASHBOARD/
│   │   ├── index.md
│   │   ├── notes.md         ← 기획 메모 (선택)
│   │   └── requirements.md  ← 화면 유저스토리 / 인수조건
│   └── EDITOR/index.md
└── flows/              ← 플로우 명세 (화면 간 유저스토리)
    └── purchase.md
```

이 디렉토리들이 없으면 생성한다.

---

## Feature 명세 (docs/features/)

feature는 **비즈니스 또는 콘텐츠 단위**이며, 반드시 **`index.md`를 가진 폴더**로 저장한다.
단독 `.md` 파일(예: `auth.md`)은 허용하지 않는다. 항상 `{name}/index.md` 구조를 사용한다.

### 재귀 feature 구조

폴더에 하위 폴더가 있더라도, 모든 feature는 필요하면 자체 기능을 가질 수 있다.

| 유형 | 조건 | `## 와이어프레임 요소` | `usedIn` |
|------|------|----------------------|----------|
| **단일 feature** | 하위 폴더 없음 | 있을 수 있음 | 있을 수 있음 |
| **상위 feature** | 하위 폴더 있음 | 있을 수도 있고 없을 수도 있음 | 있을 수도 있고 없을 수도 있음 |

- `elements`가 있으면 이 레벨 자체가 렌더링 대상이다
- 하위 `features`가 있으면 자식으로 재귀 탐색한다
- 둘 다 있으면 자체 UI와 하위 feature를 함께 표현할 수 있다
- 둘 다 없으면 불완전한 명세다

### featureId (경로 파생)

featureId는 **frontmatter에 저장하지 않는다**. 폴더 경로에서 파생한다.

1. `docs/features/` 이후 경로를 취한다
2. `/index.md`를 제거한다
3. 각 폴더명의 kebab-case를 UPPER_SNAKE_CASE로 변환한다
4. 깊이 단계는 `__`(이중 언더스코어)로 연결한다

| Path | featureId |
|------|-----------|
| `features/cart-items/index.md` | `CART_ITEMS` |
| `features/auth/login-form/index.md` | `AUTH__LOGIN_FORM` |
| `features/auth/2fa/totp/index.md` | `AUTH__2FA__TOTP` |

### 분리 기준

| 기준 | 설명 | 예시 |
|----------|-------------|---------|
| **독립 설명 가능** | 다른 feature 없이도 설명 가능한가 | "댓글"은 단독 설명 가능 |
| **재사용 가능** | 여러 화면에서 쓰이는가 | "파일 업로드"는 에디터, 설정, 메시지 등에서 사용 |
| **하나의 태스크** | 한 팀 또는 한 사람이 맡을 수 있는 개발 단위인가 | "버전관리"는 하나의 스프린트 태스크 |

- 모든 screen은 **최소 하나**의 렌더링 가능한 feature 명세를 참조해야 한다
- 하나의 feature에 여러 개발 책임이 섞이면 **명세 작성 전에 분리한다**
- 장식 전용 요소를 feature로 만들지 않는다

### 책임 단위 분해 (feature 생성 전 필수)

feature를 만들기 전에 먼저 screen을 **개발 책임 단위**로 분해한다.
이 단계가 feature 개수와 재귀 구조를 함께 결정하며, 단순 하위 기능 개수 기준의 복잡도 판단을 대체한다.

**분해 질문** (명세 작성 전에 모두 답해야 한다):

1. 이 screen 안에 **독립적으로 구현/수정 가능한 책임 영역**이 몇 개인가?
2. 한 개발 에이전트가 이 feature만 읽고도 **구현 범위를 닫을 수 있는가**?
3. 일부만 바뀌어도 **다른 부분을 함께 읽어야 하는가**? 그렇다면 상위 feature 아래로 나눌 만큼 책임이 다르지 않은 이상 하나의 feature로 유지한다.

**재귀 구조 규칙:**

| 조건 | 행동 |
|-----------|--------|
| 여러 책임 영역에 공통 규칙이나 상태가 있음 | 상위 feature `index.md`에 공통 로직을 쓴다 |
| 관련 책임들이 하나의 모듈로 구현될 예정임 | 해당 상위 feature를 모듈 경계로 본다 |
| 서로 연관된 feature가 둘 이상 같은 모듈에 속함 | 상위 feature 아래로 묶는다 |

예시 — 디자인 에디터:

```
features/
├── canvas/index.md                  ← 단일 feature (독립 책임)
├── drawing-tools/
│   ├── index.md                     ← 상위 feature (공통: 도구 전환 규칙 + 공용 기능 가능)
│   ├── shape-drawing/index.md       ← 하위 feature
│   └── text-editing/index.md        ← 하위 feature
├── panels/
│   ├── index.md                     ← 상위 feature (공통: 사이드바 열기/닫기)
│   ├── layers-panel/index.md        ← 하위 feature
│   └── properties-panel/index.md    ← 하위 feature
```

### 기본 feature 템플릿

```yaml
# Frontmatter
label: feature 이름 (한국어)
type: section | modal | drawer | dialog
usedIn:
  - docs/screens/{SCREEN}/index.md
```

섹션 순서: `## 와이어프레임 요소` → `## 상태` → `## 인터랙션` → `## 비즈니스 로직`

### 상위 feature 템플릿

```yaml
# Frontmatter
label: feature 그룹 이름 (한국어)
type: section
usedIn:
  - docs/screens/{SCREEN}/index.md
```

섹션은 `## 공통 상태`, `## 공통 인터랙션`, `## 공통 비즈니스 로직`을 필요에 따라 쓸 수 있다.

상위 feature도 자체 기능을 가질 수 있다. 자체 `## 와이어프레임 요소`가 있으면 `## 상태`와 `## 인터랙션`도 함께 쓴다. `## 상태`는 이 feature 자체 elements의 UI 상태이고, `## 공통 상태`는 하위 feature에 적용되는 공유 규칙이다. 상위 feature와 더 구체적인 하위 feature가 충돌하면, 하위 feature가 우선한다.

### Frontmatter 필드

**기본 feature:**

| 필드 | 필수 | 설명 |
|-------|----------|-------------|
| `label` | Yes | feature 이름 (한국어) |
| `type` | Yes | `section`, `modal`, `drawer`, `dialog` 등 |
| `usedIn` | No | 이 feature가 렌더링되는 screen md 경로 목록. 직접 참조 또는 상위를 통한 간접 참조 모두 포함 |

**상위 feature:**

| 필드 | 필수 | 설명 |
|-------|----------|-------------|
| `label` | Yes | feature 이름 (한국어) |
| `type` | No | `section`, `modal`, `drawer`, `dialog` 등 |
| `usedIn` | No | 이 feature가 렌더링되는 screen md 경로 목록. 직접 참조 또는 상위를 통한 간접 참조 모두 포함 |

`featureId`는 frontmatter에 두지 않는다. 경로에서 파생한다.

### `usedIn` 전파 규칙

상위 feature가 screen에 연결되면, 그 아래 모든 하위 feature도 해당 screen에서 렌더링된다. 따라서 상위 feature의 `usedIn`에 screen이 추가되면 하위 feature의 `usedIn`에도 같은 screen을 추가한다. 하위 feature가 직접 참조되든 상위를 통해 간접 참조되든 `usedIn`은 실제 렌더링되는 모든 screen을 포함해야 한다.

### 섹션 규칙

| 섹션 | 목적 | 읽는 주체 |
|---------|---------|-----------|
| **와이어프레임 요소** | 렌더링할 UI 요소 | Wireframe 스킬 |
| **상태** | 상태 변화 | Planner + Designer |
| **인터랙션** | 사용자 상호작용 | Planner + Designer |
| **비즈니스 로직** | 비즈니스 규칙 | Planner + Team |
| **requirements** | 소유 screen requirements 앵커 포인터 (screen 에 직접 연결되는 feature에 필수) | Planner + Developer Agent |
| **공통 상태/인터랙션/비즈니스 로직** | 공통 규칙 | Planner + Developer |

feature 파일은 재사용 가능한 UI/행동 블록을 설명한다. 유저스토리와 인수조건은 `docs/screens/{SCREEN_NAME}/requirements.md`에 둔다.
사전 기획 메모는 `docs/screens/{SCREEN_NAME}/notes.md`에 둔다.
프로젝트 필요에 따라 섹션을 추가하거나 제거할 수 있다.

### 요소 타입

`type` 컬럼에는 `input`, `button`, `link`, `image`, `text`, `select`, `checkbox`, `radio`, `table`, `list`를 사용한다.

---

## Screen 명세 (docs/screens/*/index.md)

screen은 페이지 단위의 **기획 문서**이며, 레이아웃과 참조 feature를 담는다.
screen은 평평한 파일이 아니라 `index.md`를 가진 **폴더**다. 각 screen은 `requirements.md`를 가져야 하고, 필요하면 `notes.md`도 가질 수 있다.

```
docs/screens/{SCREEN_NAME}/
  index.md              ← screen 정의 (레이아웃 + feature 참조)
  notes.md              ← 자유형식 기획 메모 (선택)
  requirements.md       ← screen 유저스토리 + 인수조건
```

모든 screen은 **최소 하나**의 렌더링 가능한 feature를 참조해야 한다.
하나의 screen이 같은 feature를 두 번 이상 참조하면 안 된다. 여러 위치에서 필요하면 feature를 분리한다.

### Feature 참조 문법

`@` 접두어가 붙은 마크다운 링크를 사용한다. screen은 렌더링 가능한 feature를 참조한다.
screen 명세가 폴더 안에 있으므로 `../../features/`(두 단계 상위)를 사용한다.

```markdown
[@auth](../../features/auth/index.md)
[@auth/login-form](../../features/auth/login-form/index.md)
[@auth/social-login](../../features/auth/social-login/index.md)
[@comments](../../features/comments/index.md)
```

```
✓ [@auth](../../features/auth/index.md)                       ← 상위 feature. Allowed.
✓ [@auth/login-form](../../features/auth/login-form/index.md)  ← 하위 feature. Allowed.
```

### 템플릿

```yaml
# Frontmatter
screenId: SCREEN_ID          # UPPERCASE (예: LOGIN, DASHBOARD)
title: 화면 제목              # 한국어
purpose: 한 줄 목적
viewport: pc | mobile | [pc, mobile]
```

섹션은 `## 레이아웃`(단일 viewport) 또는 `## 레이아웃 (PC)` / `## 레이아웃 (Mobile)`를 사용하고, 필요하면 `## 메모`를 둔다.

레이아웃은 번호 목록 + `[@feature](../../features/path/index.md)` 참조를 사용한다.
기획자는 **순서와 방향**만 정의한다. 시각적 디테일은 디자이너 영역이다.
유저스토리와 인수조건은 `index.md`가 아니라 `requirements.md`에 둔다.
자유형식 사전 메모는 `requirements.md`가 아니라 `notes.md`에 둔다.

### Frontmatter 필드

| 필드 | 필수 | 설명 |
|-------|----------|-------------|
| `screenId` | Yes | 대문자 screen ID (예: `LOGIN`, `DASHBOARD`) |
| `title` | Yes | 화면 제목 (한국어) |
| `purpose` | Yes | 화면 목적을 설명하는 한 문장 |
| `viewport` | Yes | `pc`, `mobile`, or `[pc, mobile]` |

---

## 워크플로우

### 새 feature 만들기

1. 대상 screen에 `notes.md`(`docs/screens/{SCREEN_NAME}/notes.md`)가 있으면 먼저 읽고, 이미 답이 있는 제약은 다시 묻지 않는다.
2. 대상 screen에 `requirements.md`(`docs/screens/{SCREEN_NAME}/requirements.md`)가 있으면 이 feature 책임에 해당하는 섹션만 읽는다.
3. 두 문서 어디에도 필요한 정보가 없으면, 사용자에게 이 feature의 역할(목적, 핵심 기능)을 묻는다.
4. **책임 점검**: 요청이 여러 개발 책임을 포함하면 상위 feature와 하위 feature 구조부터 설계한다.
5. `docs/features/{feature-name}/` 폴더와 `index.md`를 만든다. 상위 feature가 필요하면 상위 feature 템플릿을 사용한다.
6. 사용자와 함께 `## 와이어프레임 요소`를 채운다.
7. 사용자 입력에 따라 `## 상태`, `## 인터랙션`을 채운다.
8. 필요하면 `## 비즈니스 로직`을 채운다.
9. 처음에는 `usedIn`을 빈 목록으로 두고, screen이 참조할 때 갱신한다.
10. 유저스토리나 인수조건을 feature 파일에 중복해서 쓰지 않는다.

### 새 screen 만들기

모든 screen 작업은 feature 나열이 아니라 **책임 단위 분해**부터 시작한다.

아래 순서를 엄격히 지킨다.
1. 책임 분해 표를 확정한다
2. feature 구조를 확정한다
3. 사용자가 자유형식 기획 메모를 줬다면 `notes.md`를 쓰거나 갱신한다
4. `screen/index.md`를 쓴다
5. 각 `feature/index.md`를 쓴다
6. 마지막에 `requirements.md`를 쓴다
7. 마지막에 정합성 점검을 수행한다

`requirements.md`를 메모장처럼 쓰지 않는다.
feature 구조가 확정되기 전에는 `requirements.md`를 쓰지 않는다.
`requirements.md`를 쓰는 중에 새 feature를 만들지 않는다.
연결된 섹션 앵커가 있으면 `requirements.md` 전체를 다시 읽지 않는다.

**1단계 — 책임 단위 분해**

1. 사용자에게 이 screen이 무엇을 보여주는지 묻는다. (목적, 핵심 행동)
2. viewport를 **PC / 모바일 / 둘 다** 중에서 고르게 한다.
3. 책임 단위 분해를 수행한다. 각 독립 개발 단위, 범위, 상위 feature 필요 여부를 담은 **책임 분해 표**를 출력한다.
4. 진행하기 전에 책임 분해 표를 사용자에게 보여주고 확인받는다.

**2단계 — 구조 결정**

확정된 책임 분해 표를 기준으로 아래처럼 결정한다.
- **책임 단위 ≥ 3** → **다단계 모드**: feature를 하나씩 대화형으로 만든다
- **책임 단위 ≤ 2** → **단일 패스 모드**: 모든 feature와 screen 명세를 함께 만든다

두 모드 모두 분해 결과에 상위 feature가 있으면 먼저 상위 feature를 만든다.
**2.5단계 — Requirements 소유권**

feature 구조가 확정된 뒤 `docs/screens/{SCREEN_NAME}/requirements.md`를 생성하거나 갱신한다.
이 파일이 `## 유저스토리`와 `## 인수조건`의 소유 문서다.

**3a단계 — 단일 패스 모드 (≤ 2개 단위)**

1. Create folder `docs/screens/{SCREEN_NAME}/` and `index.md`
2. Create or update `notes.md` if free-form planner notes exist
3. feature 명세를 만든다
4. `[@feature]` 참조로 레이아웃을 정의한다
5. 참조된 각 feature의 `usedIn`을 갱신한다
6. Create or update `requirements.md`

**3b단계 — 다단계 모드 (≥ 3개 단위)**

분리할지 물어보지 않는다. **그냥 분리하고 공지한다.**

1. **공지**: 제안한 폴더 구조와 함께 책임 분해 표를 보여주고 다단계 모드임을 선언한다.
   ```
   "이 화면은 책임 단위 N개입니다. 하나씩 순서대로 기획합니다.
    먼저 '○○'부터 시작합니다."
   ```
2. **상위 feature 먼저 생성**: 공통 상태/인터랙션/비즈니스 로직을 담은 상위 feature `index.md`를 먼저 만든다.
3. 사용자가 원문 기획 메모를 이미 줬다면 **`notes.md`를 먼저 생성하거나 갱신**한다.
4. **골격 screen 명세 생성**: frontmatter + 자리표시자 `[@feature]` 참조를 만든다.
5. **feature를 하나씩 진행**한다. (순서: core → input → display → supporting)
   - **대화형으로** feature `index.md`를 만든다. 추정하지 않는다.
   - `usedIn`을 갱신한다.
   - 진행 상황을 알린다: **"N/M 완료. 다음은 '○○'입니다."**
6. **마무리**: screen 레이아웃 참조를 갱신하고, 그룹화된 `requirements.md`를 생성/갱신한 뒤 정합성 점검을 수행한다.

### Notes 파일

`notes.md`는 구조화 명세가 확정되기 전, 자유형식 기획 입력을 담는 screen 수준의 선택적 메모장이다.

`docs/screens/{SCREEN_NAME}/notes.md`는 아래 용도로 사용한다.
- 거친 요구사항 bullet
- 기획자가 이미 아는 정책 제약
- 열린 질문이나 엣지 케이스
- 나중에 정규화해야 하는 회의 메모 붙여넣기

규칙:
- `notes.md`는 선택 사항이다
- 자유 형식을 허용한다
- 추가 질문 전에 먼저 `notes.md`를 읽는다
- `notes.md`에 이미 답이 있으면 다시 묻지 않는다
- `notes.md`를 canonical 검증 산출물로 취급하지 않는다
- feature의 `requirements:` 포인터를 `notes.md`로 만들지 않는다

### 와이어프레임 핸드오프 (명세 변경 후 필수)

명세를 생성하거나 수정한 뒤에는 항상 **와이어프레임 핸드오프 요약**으로 마무리한다. 그래야 기획자가 변경 내용을 다시 설명하지 않고 `flowframe-wireframe`을 이어서 사용할 수 있다.

핸드오프에는 변경 범위, 변경 파일, 영향받는 screen, 권장 wireframe 액션, 추천 다음 프롬프트가 반드시 들어가야 한다.

아래 형식을 사용한다.

```markdown
## 와이어프레임 핸드오프

- 변경 범위: `feature`
- 변경 파일:
  - `docs/features/auth/login-form/index.md`
- 영향받는 화면:
  - `docs/screens/LOGIN/index.md`
- 권장 와이어프레임 액션: `partial-update`
- 추천 다음 프롬프트:
  - `flowframe-wireframe 스킬 사용해서 auth/login-form 변경을 LOGIN 와이어프레임에 반영해줘`
```

액션 선택 규칙:
- 기존 feature 요소만 변경됨 → `partial-update`
- screen 레이아웃 또는 참조 feature가 바뀜 → `screen-regenerate`
- 여러 화면에 걸친 구조 리팩터링 → `full-regenerate`

### requirements 추가

`requirements.md`는 screen 수준의 기준 검증 문서이며, 부분 읽기를 지원해야 한다.

`docs/screens/{SCREEN_NAME}/requirements.md`는 아래 구조로 작성한다.

```markdown
# {화면 제목} 요구사항

## 유저스토리

### [{책임 그룹 또는 기능군 이름}](#slug)
- {역할}으로서 {행동}하고 싶다, {목적}을 위해

## 인수조건

### [{책임 그룹 또는 기능군 이름}](#slug)
- Given {조건} When {행동} Then {결과}
```

규칙:
- 책임 단위 분해 기준으로 섹션을 그룹화한다
- 책임 그룹 제목은 갱신 후에도 안정적으로 유지한다
- 문서 상단에 각 그룹 앵커로 이동하는 짧은 인덱스를 추가한다
- 자유형식 기획 메모는 이 파일에 쓰지 않는다. 그런 내용은 `notes.md`를 사용한다
- 테스트 포인트, E2E 체크리스트, 개발자 메모를 넣지 않는다
- 텍스트를 feature 명세나 screen `index.md`에 중복하지 않는다

참조 규칙:
- `screen/index.md`는 네비게이션 용도로 `requirements.md`를 한 번 링크할 수 있다
- 전체 파일보다 연결된 책임 그룹 섹션을 먼저 읽는다
- screen 범위가 바뀌지 않았다면 연결된 섹션만 수정한다
- screen과 feature 구조가 확정된 뒤에만 `requirements.md` 초안을 작성한다
- 책임 그룹 제목에서 자동 생성된 heading anchor를 사용한다. 별도 수동 slug를 만들지 않는다

feature 소유권 규칙:
- 각 screen 에 직접 연결되는 feature 끝에 아래 한 줄 포인터를 넣는다
  - `requirements: ../../screens/{SCREEN_NAME}/requirements.md#auto-heading-anchor`
- 이 포인터는 대응되는 책임 그룹 heading의 자동 생성 앵커를 가리켜야 한다
- 단순 이동 포인터일 뿐이며, US/AC 텍스트를 중복해서 적으면 안 된다

`requirements.md`를 작성하거나 갱신한 뒤에는 작업을 끝내기 전에 반드시 정합성 점검을 한 번 수행한다.

### feature 수정

1. 기존 feature `index.md`를 읽는다
2. 사용자 변경사항을 반영한다
3. 와이어프레임 요소가 바뀌었으면 아래를 안내한다
   "와이어프레임 요소가 변경되었습니다. 와이어프레임도 업데이트하시겠습니까?"
4. 변경이 feature의 사용자 목표나 검증 행동에 영향을 주면, 각 소유 screen의 `requirements.md`에서 대응 섹션만 갱신한다
5. `usedIn`을 확인하고 아래를 안내한다
   "다음 화면의 requirements도 검토가 필요할 수 있습니다: {screen 목록}"
6. `와이어프레임 핸드오프` 요약을 출력한다

### screen에 feature 추가/제거

- 추가: feature 참조를 넣고, `usedIn`을 갱신한 뒤 `screen-regenerate` 핸드오프를 출력한다
- 제거: feature 참조를 삭제하고, `usedIn`을 갱신하고, wireframe 업데이트 필요를 경고한 뒤 `screen-regenerate` 핸드오프를 출력한다

### feature 삭제

1. feature의 `usedIn` 목록을 확인한다
2. 아직 참조하는 screen이 있으면 경고한다
3. 확인을 받은 뒤 참조를 제거하고 feature 폴더를 삭제하며, wireframe 업데이트 필요를 경고한다
4. `screen-regenerate` 핸드오프를 출력한다

### feature를 상위 feature로 확장

feature가 너무 커지면 기존 feature를 유지한 채 하위 feature를 추가한다.

1. 기존 `index.md`에서 공통 규칙과 그대로 유지할 기능을 구분한다
2. 공통 규칙은 `## 공통 상태`, `## 공통 인터랙션`, `## 공통 비즈니스 로직`으로 정리한다
3. 하위 feature 폴더를 만들고, 각각의 `index.md`와 요소 하위 집합을 작성한다
4. 상위 feature를 계속 screen이 참조할지, 하위 feature를 직접 참조할지 결정하고 `usedIn`을 맞춘다
5. wireframe `data-feature` ID를 새 구조에 맞게 갱신한다
6. `structure` 범위의 `와이어프레임 핸드오프` 요약과 `screen-regenerate`를 출력한다

### screen 삭제

1. screen `index.md`를 읽고 참조된 feature를 모두 찾는다
2. 각 참조 feature의 `usedIn`에서 이 screen을 제거한다
3. screen **폴더**를 삭제한다 (`index.md`, `notes.md`, `requirements.md` 포함)
4. 대응하는 wireframe이 있으면 함께 삭제할지 묻는다

### flow 생성

유저스토리가 여러 screen에 걸치면 아래처럼 처리한다.

1. `docs/flows/{flow-name}.md`를 만든다
2. 아래 flow 템플릿을 사용한다

```markdown
---
flowId: FLOW_ID
title: 플로우 제목
screens:
  - docs/screens/SCREEN_A/index.md
  - docs/screens/SCREEN_B/index.md
---

# 플로우 제목

## 유저스토리

- {역할}으로서 {행동}하고 싶다, {목적}을 위해

## 인수조건

- Given {화면A 조건} When {행동} Then {화면B 결과}
```

3. 실제로 여러 screen에 걸치는 스토리에만 flow를 만든다

### 정합성 점검

트리거: "전체 정합성 확인해줘", "정합성 확인"

아래를 검증한다.

1. **Feature 참조 점검**: screen의 모든 `[@feature]`에 대응하는 feature `index.md`가 있는가
2. **usedIn 동기화 점검**: 모든 참조 feature의 `usedIn`이 실제 screen 참조와 일치하는가
3. **Wireframe 동기화 점검**: 모든 wireframe의 `data-feature` ID가 feature의 와이어프레임 요소와 일치하는가
4. **notes 사용 점검**: screen에 자유형식 메모가 있으면 `requirements.md`가 아니라 `notes.md`에 있는가
5. **requirements 존재 점검**: 기획된 모든 screen에 `requirements.md`가 있는가
6. **requirements 구조 점검**: `requirements.md`에 `## 유저스토리`, `## 인수조건`, 안정적인 책임 그룹 제목이 있는가
7. **requirements 네비게이션 점검**: screen 에 직접 연결된 feature에 `requirements:` 포인터가 있고, screen requirements의 자동 생성 앵커를 가리키는가
8. **feature에 story/AC 중복 없음**: feature에 `## 유저스토리` 또는 `## 인수조건`이 중복 작성되지 않았는가
9. **frontmatter에 featureId 없음**: feature frontmatter에 `featureId`가 없어야 한다
10. **Screen은 렌더링 가능한 feature 참조**: screen `[@...]`가 `elements` 또는 하위 `features`를 가진 feature를 가리키는가
11. **상위 feature도 기능 가능**: 하위 feature가 있는 `index.md`에 `## 와이어프레임 요소`가 있어도 오류로 취급하지 않는다

불일치는 표 형태로 보고한다.

| 유형 | 파일 | 이슈 |
|------|------|------|
| 참조 누락 | docs/screens/EDITOR/index.md | `@file-tree`가 참조되지만 대응하는 feature `index.md`가 존재하지 않는다 |
| `usedIn` 불일치 | docs/features/auth/login-form/index.md | `usedIn`에 `EDITOR/index.md`가 있지만 실제 참조는 없다 |
| notes 위치 오류 | docs/screens/LOGIN/requirements.md | 자유형식 기획 메모가 `requirements.md`에 작성되어 있다. `notes.md`로 옮겨야 한다 |
| requirements 누락 | docs/screens/LOGIN/requirements.md | screen 유저스토리 / 인수조건 파일이 없다 |
| requirements 앵커 오류 | docs/features/auth/login-form/index.md | `requirements` 포인터가 존재하지 않는 앵커를 가리킨다 |
| 중복 규칙 위반 | docs/features/comments/index.md | feature에 아직 `## 유저스토리` 섹션이 남아 있다 |
| 잘못된 참조 | docs/screens/LOGIN/index.md | `@auth`가 `## 와이어프레임 요소`도 하위 feature도 없는 feature를 가리킨다 |
| 남은 `featureId` | docs/features/comments/index.md | frontmatter에 `featureId`가 아직 남아 있어 제거해야 한다 |

---

## 가이드라인

- 명세는 **한국어**로 작성한다. (기획자의 언어를 따른다)
- `screen/index.md`는 구조와 feature 참조를 정의한다
- `screen/requirements.md`는 screen 유저스토리와 인수조건의 기준 문서다
- `screen/notes.md`는 구조 확정 전 기획 메모용 선택 문서다
- feature 파일에는 유저스토리나 인수조건을 넣지 않는다
- feature 명세는 재사용 가능한 블록이어야 하며, screen 수준 검증 텍스트를 복사하지 않고도 읽혀야 한다
- 유저스토리 형식: `{role}으로서 {action}하고 싶다, {goal}을 위해`
- 인수조건 형식: 조건-행동-결과
- 여러 책임이 있으면 `requirements.md`에서 책임 그룹별로 유저스토리와 인수조건을 묶는다
- 화면 간 흐름은 `docs/flows/`에 둔다
- 항상 `usedIn` 정합성을 유지한다
- **사용자 설명이 모호하면 작성 전에 확인 질문을 한다. 추정으로 채우지 않는다**
- 처음부터 모든 섹션을 채울 필요는 없다. 최소 기준은 `## 와이어프레임 요소`다
- 모든 screen은 최소 하나의 렌더링 가능한 feature를 가져야 한다
- 한 개발 에이전트가 범위를 닫을 수 있을 정도의 크기를 선호한다. 시각 블록이나 단일 요소 기준으로 쪼개지 않는다

### 파일명과 확인 원칙

- Feature 폴더는 영어 **kebab-case** 를 사용하고 파일은 `index.md`만 둔다
- Screen 폴더는 **대문자 screen ID** 를 사용하고, `index.md`, 선택적 `notes.md`, `requirements.md`를 둔다
- 비즈니스 로직, 임계값, 에러 처리, 권한 차이는 추정하지 말고 기획자에게 묻는다
- 템플릿 형식, `usedIn` 동기화, 경로 파생 `featureId` 규칙은 기본 전제로 사용해도 된다
