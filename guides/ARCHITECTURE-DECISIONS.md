# Architecture Decisions

> FlowFrame Wireframe Skill의 핵심 설계 결정 사항을 기록한다.
> 이 문서는 SKILL.md 업데이트 시 단일 소스(Single Source of Truth)로 사용한다.
> 재귀적 폴더 구조의 상세 명세는 `guides/FEATURE-FOLDER-SPEC.md`를 참조한다.

---

## 1. 파일 구조: Features + Screens + Flows + Wireframes

### 결정

사용자 프로젝트에서 아래 4계층 구조를 사용한다. Feature는 **폴더** 단위로 관리하며, 각 폴더에 `index.md`를 둔다.

```
project/
└── docs/
    ├── features/                  ← 기능 단위 명세 (재사용 가능, 폴더 구조)
    │   ├── auth/                  ← 상위 feature (하위 피쳐 + 자체 요소 가능)
    │   │   ├── index.md           ← 공통 상태/인터랙션/비즈니스 로직 + 공용 요소 가능
    │   │   ├── login-form/
    │   │   │   └── index.md       ← 하위 feature (와이어프레임 요소 포함)
    │   │   ├── social-login/
    │   │   │   └── index.md       ← 하위 feature
    │   │   └── signup/
    │   │       └── index.md       ← 하위 feature
    │   ├── comments/              ← 단일 feature (하위 피쳐 없음)
    │   │   └── index.md
    │   ├── version-control/
    │   │   └── index.md           ← 단일 feature
    │   ├── file-upload/
    │   │   └── index.md           ← 단일 feature
    │   └── notifications/
    │       └── index.md           ← 단일 feature
    │
    ├── screens/                   ← 화면 정의 + 화면별 와이어프레임
    │   ├── LOGIN/
    │   │   ├── index.md           ← 화면명세
    │   │   ├── notes.md           ← 선택. 기획자가 미리 적어둔 요구사항 메모
    │   │   ├── requirements.md    ← screen 유저스토리 + 인수조건
    │   │   └── wireframe.html     ← 단일 viewport 와이어프레임
    │   ├── DASHBOARD/
    │   │   ├── index.md
    │   │   └── wireframe.html
    │   └── EDITOR/
    │       ├── index.md
    │       ├── wireframe-pc.html
    │       └── wireframe-mobile.html
    │
    ├── flows/                     ← 화면 간 사용자 흐름과 인수조건
    │   ├── purchase.md
    │   └── onboarding.md
```

### 근거

- 기획자가 기능, 화면, 화면 간 흐름을 분리해서 관리할 수 있다
- 기능 md는 여러 화면에서 재사용 가능하다
- flow md는 여러 화면에 걸친 유저스토리와 인수조건을 별도로 관리할 수 있다
- 와이어프레임은 화면 단위로 생성하여 한눈에 볼 수 있다
- 폴더 구조를 사용하면 기능이 커져도 비즈니스 경계를 깨지 않고 하위 폴더로 확장할 수 있다

---

## 2. 기능(Feature) 분리 기준

### 결정

기능 하나의 폴더는 **비즈니스 기능 단위**로 나눈다. UI 요소 단위가 아니다. 기능이 커지면 폴더 내부에 하위 폴더를 추가하여 비즈니스 경계를 유지하면서 구조적으로 확장할 수 있다. 상위 feature도 자체 `elements` 와 하위 `features` 를 동시에 가질 수 있다.

### 기준표

| 기준 | 설명 | 예시 |
|------|------|------|
| **독립 설명 가능** | 다른 기능 없이 혼자 설명 가능한가 | "댓글" 기능은 독립적으로 설명 가능 |
| **재사용 가능** | 여러 화면에서 쓰이는가 | "파일 업로드"는 에디터, 설정, 메시지 등에서 사용 |
| **하나의 태스크** | 한 팀/한 사람이 맡을 수 있는 개발 단위인가 | "버전관리"는 하나의 스프린트 태스크 |

### 올바른 분리 예시

```
✅ 좋은 예 (비즈니스 기능 단위 — 폴더 구조)
├── auth/                       ← 상위 feature: 로그인 + 회원가입 + 소셜로그인 + 비밀번호 찾기
│   ├── index.md                ← 공통 상태/인터랙션/비즈니스 로직 + 공용 요소 가능
│   ├── login-form/
│   │   └── index.md            ← 하위 feature
│   ├── social-login/
│   │   └── index.md            ← 하위 feature
│   └── signup/
│       └── index.md            ← 하위 feature
├── comments/                   ← 단일 feature: 작성 + 수정 + 삭제 + 멘션 + 답글 (아직 작음)
│   └── index.md
├── version-control/            ← 단일 feature: 히스토리 + 롤백 + 버전 비교
│   └── index.md
├── file-upload/                ← 단일 feature: 드래그앤드롭 + 미리보기 + 용량제한
│   └── index.md
└── notifications/              ← 단일 feature: 실시간 알림 + 설정 + 읽음처리
    └── index.md

✗ 나쁜 예 (UI 요소 단위 — 너무 세분화)
├── email-input/
│   └── index.md
├── password-input/
│   └── index.md
├── submit-button/
│   └── index.md
├── social-google-btn/
│   └── index.md
└── social-github-btn/
    └── index.md
```

### 성장 경로

기능이 커질 때 폴더 구조는 비즈니스 경계를 유지한 채 확장할 수 있다:

```
초기:  comments/index.md          ← 모든 요소가 한 파일에

성장 후:
  comments/
    index.md                              ← 상위 feature: 공통 규칙 + 공용 요소 가능
    thread/index.md                       ← 하위 feature: 스레드 관련 요소
    mentions/index.md                     ← 하위 feature: 멘션 관련 요소
    reactions/index.md                    ← 하위 feature: 리액션 관련 요소
```

비즈니스 경계(`comments`)는 그대로 유지되고, 내부 구조만 세분화된다.

---

## 3. 화면(Screen) md 역할

### 결정

화면 md는 **화면 단위 기획 문서**로 사용한다. screen은 **폴더 단위**로 관리하며, `docs/screens/{SCREEN_NAME}/index.md` 구조를 사용한다. 레이아웃, 기능 참조, 화면 연계 인수조건을 함께 담고, 기능의 상세 구현 명세는 docs/features/ 에 둔다.

### screen 폴더 구조

```
docs/screens/
  {SCREEN_NAME}/
    index.md              ← 화면명세 (레이아웃 + 화면 연계 AC)
    notes.md              ← 선택. 기획자가 미리 적어둔 요구사항 메모
    requirements.md       ← screen 유저스토리 + 인수조건
```

- 모든 screen은 **폴더**다. 단독 `.md` 파일(예: `CART.md`)은 허용하지 않는다
- 모든 폴더에는 반드시 `index.md`가 있다
- 폴더 이름은 **대문자** (기존 screenId 규칙과 동일: `CART`, `LOGIN`, `PRODUCT_LIST`)

### notes.md

기획자가 feature 작성 전에 미리 적어두는 **자유형식 요구사항 메모**. 형식 제한 없이 기획자가 자유롭게 작성한다. 스킬은 feature 작성 시 해당 screen의 `notes.md`를 **먼저 읽고** 반영한다. `notes.md`가 있으면 이미 답이 있는 내용은 질문하지 않는다. `notes.md`가 없으면 기존대로 질문한다.

### requirements.md

screen 단위의 **유저스토리 + 인수조건** 문서다. 자유형식 메모가 아니라 구조화된 검증 문서로 사용한다.

### 화면 md 예시

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 서비스에 로그인하는 화면
viewport: [pc, mobile]
---

## 레이아웃 (PC)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 레이아웃 (Mobile)

1. 전체 화면 — [@auth/login-form](../../features/auth/login-form/index.md)

## 화면 연계 인수조건

- Given 소셜 로그인 성공 When 최초 로그인 Then 추가 정보 입력 폼 표시
```

### 근거

- 화면 단위 목표와 검증 기준을 한 문서 안에서 볼 수 있다
- 기능 상세 구현은 feature md로 분리되어 중복을 줄인다
- 폴더 구조를 사용하면 `notes.md`, `requirements.md` 같은 보조 문서를 자연스럽게 함께 관리할 수 있다
- 유저스토리와 인수조건은 screen 폴더의 `requirements.md`에 작성하고, screen `index.md`는 레이아웃 중심으로 유지한다

---

## 3-1. 플로우(Flow) md 역할

### 결정

여러 화면에 걸친 사용자 흐름은 docs/flows/ 아래의 flow md로 분리한다. 단일 화면 안에서 닫히는 상호작용은 screen md에 남긴다.

### flow md 예시

```markdown
---
flowId: PURCHASE
title: 구매 플로우
screens:
  - docs/screens/PRODUCT_LIST/index.md
  - docs/screens/CART/index.md
  - docs/screens/CHECKOUT/index.md
---

# 구매 플로우

## 유저스토리

- 사용자로서 상품을 탐색하고 장바구니에 담아 결제까지 완료하고 싶다, 구매를 마치기 위해

## 인수조건

- Given 상품 목록 화면에서 상품을 선택하면 When 장바구니에 추가할 때 Then 장바구니 화면에서 해당 상품이 보여야 한다
```

### 근거

- 화면 간 유저스토리와 인수조건을 screen md에 과도하게 중복 작성하지 않아도 된다
- 단일 화면 정책과 다화면 흐름 정책을 분리할 수 있다
- 추후 테스트와 리뷰 단위도 screen 과 flow 로 나눠 관리하기 쉽다

---

## 4. 와이어프레임과 명세 연결

### 결정

와이어프레임은 **시각적 구조만** 간결하게 보여준다. 상세 명세는 flowframe-meta의 `spec` 필드로 연결한다. `featureId`는 폴더 경로에서 자동 파생하며, 깊이 구분자로 더블 언더스코어(`__`)를 사용한다.

### 메타데이터 확장

```json
{
  "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
  "featureId": "AUTH__LOGIN_FORM",
  "type": "input",
  "label": "이메일",
  "description": "이메일 입력 필드",
  "spec": "../../features/auth/login-form/index.md"
}
```

### featureId 경로 파생 규칙

| 경로 | featureId |
|------|-----------|
| `docs/features/cart-items/index.md` | `CART_ITEMS` |
| `docs/features/auth/index.md` | `AUTH` |
| `docs/features/auth/login-form/index.md` | `AUTH__LOGIN_FORM` |
| `docs/features/auth/2fa/totp/index.md` | `AUTH__2FA__TOTP` |

- 단일 언더스코어(`_`): 단어 연결 (예: `CART_ITEMS`)
- 더블 언더스코어(`__`): 깊이 구분 (예: `AUTH__LOGIN_FORM`)

### 역할 분담

| 산출물 | 역할 | 보는 사람 |
|--------|------|-----------|
| **와이어프레임 (HTML)** | 화면 구조, 레이아웃, 기능 배치 | 전원 (기획자, 디자이너, 개발자) |
| **기능 명세 (docs/features/\*/index.md)** | 상태, 인터랙션, 비즈니스 로직, API 명세 | 디자이너, 개발자 |
| **화면 정의 (docs/screens/\*/index.md)** | 레이아웃, 기능 조합, 화면 연계 인수조건 | 기획자 |
| **플로우 정의 (docs/flows/\*.md)** | 여러 화면에 걸친 유저스토리와 인수조건 | 기획자 |

### 근거

- 와이어프레임 = 지도 (한눈에 구조 파악)
- 기능 명세 = 상세 설명서 (필요할 때 참조)
- FlowFrame이 `spec` 필드를 지원하면 클릭으로 명세 이동 가능
- 지원 안 해도 경로가 메타데이터에 있으므로 개발자가 찾아갈 수 있음

---

## 5. 기능 명세(Feature) md 템플릿

### 결정

기능 명세 md는 재귀 구조를 가진다. feature는 `elements`, 하위 `features`, 또는 둘 다 함께 가질 수 있다. `featureId`는 frontmatter에 쓰지 않으며 폴더 경로에서 자동 파생한다.

### 기본 feature 템플릿

렌더링 가능한 feature는 `## 와이어프레임 요소`를 가진다. 와이어프레임 스킬은 이 섹션을 UI 생성의 주요 소스로 사용한다.

```markdown
---
label: 댓글
type: section
usedIn:
  - docs/screens/EDITOR/index.md
  - docs/screens/DASHBOARD/index.md
  - docs/screens/REVIEW/index.md
---

# 댓글

## 와이어프레임 요소

화면에 그릴 UI 요소 목록. 에이전트는 이 섹션만 보고 와이어프레임을 그린다.

| 요소 | type | 설명 |
|------|------|------|
| 댓글 목록 | list | 작성자, 시간, 내용 표시 |
| 댓글 입력 | input | 텍스트 입력 영역 |
| 등록 버튼 | button | 댓글 등록 |
| 답글 버튼 | button | 각 댓글에 답글 달기 |
| 삭제 버튼 | button | 본인 댓글 삭제 |
| 멘션 | text | @사용자명 자동완성 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 댓글 목록 표시, 입력창 비어있음 |
| 빈 상태 | "아직 댓글이 없습니다" 메시지 |
| 로딩 | 스켈레톤 UI |
| 에러 | "댓글을 불러오지 못했습니다" + 재시도 |

## 인터랙션

- 등록 버튼 클릭 → 댓글 목록 상단에 추가
- 답글 버튼 클릭 → 해당 댓글 아래 입력창 표시
- 삭제 버튼 클릭 → 확인 다이얼로그 → 삭제
- @ 입력 → 멘션 자동완성 드롭다운

## 비즈니스 로직

- 로그인한 사용자만 댓글 작성 가능
- 본인 댓글만 삭제 가능
- 댓글 최대 1000자
- 멘션된 사용자에게 알림 발송

requirements: ../../screens/EDITOR/requirements.md#댓글

```

### 상위 feature 템플릿

상위 feature는 자식 폴더가 있는 피쳐다. 필요하면 자체 `elements` 와 `usedIn` 을 가질 수 있다. `elements`가 없더라도 하위 `features`가 있으면 와이어프레임 스킬은 재귀적으로 내려간다. 기획자/개발자는 이 파일을 공통 도메인 컨텍스트로 참고한다.

```markdown
---
label: 인증
type: section
usedIn:
  - docs/screens/LOGIN/index.md
---

# 인증

## 와이어프레임 요소

| 요소 | type | 설명 |
|------|------|------|
| 인증 상태 배너 | text | 현재 인증 상태와 세션 만료 여부를 요약 표시 |

## 공통 상태

| 상태 | 설명 |
|------|------|
| 미인증 | 로그인 필요 |
| 인증됨 | 세션 활성 |
| 세션 만료 | 재로그인 필요 |

## 공통 인터랙션

- 세션 만료 시 → 재로그인 모달 표시

## 공통 비즈니스 로직

- 세션 유효시간 30분, 갱신 가능
- 모든 인증 실패는 보안 로그 기록
- HTTPS 필수
```

### 기본 feature 프론트매터 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 |
| `type` | Yes | 기능 유형 (`section`, `modal`, `drawer` 등) |
| `usedIn` | No | 이 기능을 사용하는 화면 md 경로 목록. screen 에서 직접 참조되는 feature 에만 필요하다 |

**`featureId`는 frontmatter에 없다.** 폴더 경로에서 자동 파생한다.

### 상위 feature 프론트매터 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 |
| `type` | No | 기능 유형 (`section`, `modal`, `drawer` 등) |
| `usedIn` | No | 이 기능을 직접 사용하는 화면 md 경로 목록 |

**`featureId` 필드는 없다.**

### 읽는 주체와 역할

| 주체 | 직접 참조된 feature | 상위 feature |
|------|--------------------|------------|
| **wireframe 스킬** | `## 와이어프레임 요소`를 읽고 렌더링 | 하위 `features`가 있으면 재귀 탐색, `elements`가 있으면 함께 렌더링 |
| **spec/planner/developer** | 상태, 인터랙션, 비즈니스 로직 참고 | ancestor feature의 `## 공통 *` 섹션을 함께 참고 |

### 병합 규칙

상위 feature의 공통 섹션과 더 구체적인 하위 feature의 섹션이 충돌하면, 하위 feature 규칙을 우선한다.

### 섹션별 역할 (feature 기준)

| 섹션 | 읽는 주체 | 용도 |
|------|-----------|------|
| **프론트매터 (`usedIn`)** | 에이전트 (수정 영향 범위 파악) | 어떤 화면의 와이어프레임을 업데이트할지 결정 |
| **와이어프레임 요소** | 에이전트 (와이어프레임 렌더링 시) | HTML의 `data-feature-id` / `data-feature` 영역과 metadata `features[].elements` 항목 생성 |
| **상태** | 기획자 + 디자이너 | 상태별 UI 설계 |
| **인터랙션** | 기획자 + 디자이너 | 사용자 흐름과 반응 설계 |
| **비즈니스 로직** | 기획자 + 팀 | 정책, 조건, 제약 정리 |

### 근거

- 에이전트가 `elements`가 있는 feature는 직접 렌더링하고, 하위 `features`가 있으면 재귀적으로 탐색하여 빠르게 렌더링
- 상위 feature는 `data-feature-id` 래퍼와 재귀 metadata 구조를 통해 화면 안에서 그룹을 형성할 수 있음
- 기능 수정 시 screen md를 거치지 않고 feature md → wireframe HTML 직접 업데이트 가능
- 나머지 섹션은 협업 참고용으로, 와이어프레임 생성과 무관
- feature depth는 자식 폴더 유무로 자동 결정되며, 렌더링 여부는 `elements` / 하위 `features` 보유 여부로 판단

---

## 6. 에이전트 + 스킬 역할 분리

### 결정

기획 도우미 **에이전트**와 와이어프레임 생성 **스킬**을 분리한다.

### 역할

| 구분 | 역할 | 담당 |
|------|------|------|
| **에이전트** | 기획자와 대화하며 docs/features/\*/index.md, docs/screens/\*/index.md, docs/flows/\*.md 작성/수정 | 기획 도메인 지식, 템플릿 가이드 |
| **스킬** | md를 읽고 각 screen 폴더 안의 `wireframe*.html` 생성/업데이트 | FlowFrame 규격, HTML 렌더링 규칙 |

### 근거

- 에이전트는 기획 맥락을 유지하고, 스킬은 렌더링 규칙만 담당 → 각자 가벼움
- 스킬은 40+ 에이전트에서 범용 사용 가능
- 에이전트 없이 스킬만으로도 수동 와이어프레임 생성 가능

---

## 7. 와이어프레임 생성/업데이트 워크플로우

### 결정

- 와이어프레임 업데이트는 **자동이 아닌 사용자 명시적 요청** 시에만 실행한다
- 사용자는 **자연어**로 뭘 고쳤는지 말하면 된다 (파일 경로, git 지식 불필요)
- 에이전트는 영향 범위를 보여주고 **사용자 컨펌을 받은 후** 실행한다

### 근거

- 자동 sync는 자잘한 수정마다 토큰을 소모하므로 비효율적
- 기획자가 여러 md를 수정한 뒤 "됐다" 싶을 때 한번에 돌리는 게 자원 효율적
- 기획자는 git이나 파일 경로를 몰라도 됨 — 자연어로 소통

### 최초 생성 (2-pass)

```
Pass 1: screen md 읽기 → 레이아웃 + 기능 위치 결정
Pass 2: screen 이 참조한 feature md를 하나씩 읽기 → 해당 위치에 와이어프레임 요소 렌더링
  → feature 에 `elements`가 있으면 해당 레벨 UI 그리기
  → 하위 `features`가 있으면 재귀적으로 내려가며 그리기
  → 필요한 섹션만 읽기
```

### 업데이트 흐름 (사용자 트리거 + 컨펌)

```
기획자: "댓글이랑 로그인 폼 수정했어, 와이어프레임 업데이트해줘"
   ↓
에이전트: docs/features/comments/index.md,
        docs/features/auth/login-form/index.md 찾기
        → 각 파일의 usedIn 확인
   ↓
에이전트: 영향 범위 표시 + 컨펌 요청

  "다음 와이어프레임이 영향 받습니다:
   - EDITOR.html (댓글, 로그인 폼)
   - LOGIN.html (로그인 폼)
   - DASHBOARD.html (댓글)

   진행할까요?"
   ↓
기획자: "LOGIN은 빼고 나머지만"
   ↓
에이전트 → 스킬 호출:
  1. docs/features/comments/index.md의 "와이어프레임 요소" 읽기
  2. docs/features/auth/login-form/index.md의 "와이어프레임 요소" 읽기
  3. EDITOR.html에서 해당 `data-feature-id` / `data-feature` 영역 교체
  4. DASHBOARD.html에서 해당 `data-feature-id` / `data-feature` 영역 교체
     (screen md 안 읽음, 다른 feature md 안 읽음)
```

### 파일 매칭 규칙

- `docs/screens/LOGIN/index.md` → `docs/screens/LOGIN/wireframe.html`
- `docs/screens/EDITOR/index.md` + `viewport: [pc, mobile]` → `docs/screens/EDITOR/wireframe-pc.html`, `docs/screens/EDITOR/wireframe-mobile.html`
- feature 폴더 경로 → featureId 자동 파생 → 메타데이터의 `featureId` 및 `spec` 필드로 연결
- HTML feature 래퍼는 `data-feature-id="{featureId}"`, `data-feature-label="{label}"` 패턴을 사용
- HTML의 `data-feature`는 `FEATURE_{FEATURE_ID}_{ELEMENT_ID}` 패턴 (예: `FEATURE_AUTH__LOGIN_FORM_EMAIL`)
- 업데이트 시 해당 `data-feature-id` / `data-feature` 영역만 교체
- screen md에서 feature 참조: `[@auth](../../features/auth/index.md)`, `[@auth/login-form](../../features/auth/login-form/index.md)` 형식 모두 가능하다. screen이 폴더 안이므로 `../../`를 사용한다.

---

## 8. PC/모바일 뷰포트 처리

### 결정

한 화면 md에서 `viewport` 섹션을 분기한다. 화면 명세는 하나로 유지하되, 와이어프레임 HTML은 뷰포트별로 별도 파일을 생성한다.

### 레이아웃 기술 수준

기획자는 **정보 배치 순서 + 영역 분할 방향**만 기술한다. 간격/크기/비율 등 시각 디테일은 디자이너 영역이다.

| 담당 | 범위 |
|------|------|
| **기획자** | 정보 배치 순서, 영역 분할 방향 (좌/우, 상/하) |
| **디자이너** | 간격, 크기, 비율, 시각 디테일 |

### 화면 md 예시

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 서비스에 로그인하는 화면
viewport: [pc, mobile]
---

## 레이아웃 (PC)
1. 좌: 브랜딩 이미지 | 우: 인증 폼 — [@auth/login-form](../../features/auth/login-form/index.md)
2. 하단: 소셜 로그인 — [@auth/social-login](../../features/auth/social-login/index.md)

## 레이아웃 (Mobile)
1. 로고
2. 인증 폼 — [@auth/login-form](../../features/auth/login-form/index.md)
3. 소셜 로그인 — [@auth/social-login](../../features/auth/social-login/index.md)
```

### 근거

- 파일 수가 늘어나지 않아 관리 부담 적음
- 기획자가 순서와 방향만 적으므로 작성 부담 최소
- 같은 화면의 PC/모바일을 한눈에 비교 가능
- 스킬이 viewport 섹션별로 파싱하여 각각 렌더링 가능

---

## 9. feature 확장 절차

### 결정

기능이 커지면 기존 feature를 상위 feature로 유지하면서 하위 feature를 추가한다. 비즈니스 경계(최상위 폴더)는 유지하면서 내부 구조만 세분화하는 방식이다.

### 승격 전

```
auth/
  index.md        ← 단일 feature. 와이어프레임 요소 13개. 너무 커짐.
```

### 승격 후

```
auth/
  index.md        ← 상위 feature로 확장 (공통 섹션 추가, 공용 요소 유지 가능)
  login-form/
    index.md      ← 하위 feature. 기존 요소 중 로그인 관련
  social-login/
    index.md      ← 하위 feature. 기존 요소 중 소셜 관련
  signup/
    index.md      ← 하위 feature. 기존 요소 중 회원가입 관련
```

### 승격 체크리스트

1. 기존 feature `index.md`에서 공통으로 남길 규칙과 하위로 분리할 요소를 구분한다
2. 기존 상태/인터랙션/비즈니스 로직 중 공통 부분은 상위 feature에 남기고, 필요하면 섹션 이름에 `공통` 접두어를 부여한다
3. 하위 feature 폴더를 생성하고 각각에 해당 와이어프레임 요소를 이동한다
4. screen 이 상위 feature를 계속 참조할지, 하위 feature를 직접 참조할지 결정하고 `usedIn`을 맞춘다
5. wireframe metadata와 `data-feature` ID가 실제 참조 구조와 일치하도록 갱신한다

### 근거

- 기능이 성장해도 최상위 비즈니스 경계(`auth`, `comments` 등)가 깨지지 않는다
- 상위 feature와 하위 feature를 상황에 맞게 참조할 수 있어 재귀 구조를 유지한 채 설계를 세분화할 수 있다
- 승격은 점진적이다 — 모든 feature를 처음부터 분할할 필요 없이 필요할 때 승격하면 된다

상세 명세: `guides/FEATURE-FOLDER-SPEC.md`

---

## 10. 스킬 구성과 에이전트 관계

### 결정

이 프로젝트는 **2개의 스킬**을 제공한다. 별도 에이전트는 만들지 않는다.

### 스킬 구성

```
사용자 or 외부 에이전트 (누구든)
        │
        ▼
   ┌──────────────────┐
   │  flowframe-spec  │  ← 스킬 1: 기획 명세 작성/수정
   │  대화 → md 생성   │
   └──────────────────┘
        │
        │  docs/features/*/index.md, docs/screens/*/index.md, docs/flows/*.md
        │
        ▼
   ┌─────────────────────────┐
   │  flowframe-wireframe    │  ← 스킬 2: 와이어프레임 생성
   │  md → docs/screens/*/wireframe*.html │
   └─────────────────────────┘
```

| 스킬 | 역할 | 트리거 예시 |
|------|------|------------|
| **flowframe-spec** | 기획자와 대화하며 docs/features/\*/index.md, docs/screens/\*/index.md, docs/flows/\*.md 작성/수정 | "기획서 만들어줘", "기능 추가", "화면 명세", "플로우 정리" |
| **flowframe-wireframe** | md를 읽고 각 screen 폴더 안의 `wireframe*.html` 생성/업데이트 | "와이어프레임 만들어줘", "와이어프레임 업데이트" |

- 에이전트가 스킬을 호출하든, 사용자가 직접 호출하든 동일하게 동작
- 에이전트는 이 프로젝트 범위 밖 — 스킬만 범용으로 제공

### 근거

- 기획 명세와 와이어프레임 생성을 분리하여 각 스킬이 가볍고 독립적
- 스킬은 범용이므로 어떤 에이전트/워크플로우에서든 사용 가능
- 에이전트 구현은 사용처마다 다르므로 스킬과 결합하지 않음

---

## 11. 열린 질문 (추후 결정)

| 질문 | 배경 |
|------|------|
| FlowFrame `spec` 필드 지원 여부 | FlowFrame 측과 협의 필요 |
| 훅 기반 자동 트리거 | 추후 Claude Code 훅으로 자동화 가능하나 현재는 수동 트리거 |
| 기능 명세 섹션 커스터마이즈 | 프로젝트마다 필요한 섹션이 다를 수 있음 (QA 체크리스트, 접근성 등) |
