# Example: Screen Spec — 에디터

```markdown
---
screenId: EDITOR
title: 에디터
purpose: 사용자가 콘텐츠를 편집하고 팀원과 협업하는 메인 작업 화면
viewport: pc
---

# 에디터

## 레이아웃

1. 상단 메뉴바 — 파일명, 저장 상태, 공유 버튼
2. 좌측 사이드바 — [@file-tree](../../features/file-tree/index.md)
3. 메인 편집 영역 — [@editor-canvas](../../features/editor-canvas/index.md)
4. 우측 패널
   - [@comments](../../features/comments/index.md)
   - [@version-control](../../features/version-control/index.md)
5. 하단 상태바 — 커서 위치, 줌 레벨, 협업자 아바타

<!-- 기능별 유저스토리와 인수조건은 각 feature leaf에 작성되어 있다. 여기에는 기능 간 연동 조건만 작성한다. -->

## 화면 연계 인수조건

- Given 좌측 파일 트리에서 파일을 클릭하면 Then 메인 편집 영역에 해당 파일 내용이 로드된다
- Given 메인 편집 영역에서 텍스트를 선택하고 When 댓글 추가를 누르면 Then 우측 댓글 패널에 선택 범위가 연결된 댓글 입력창이 열린다
```

## Key patterns

- Screen spec is the **planning document** — layout + cross-feature acceptance criteria
- No feature implementation details here (those live in `docs/features/{name}/index.md`). Screen is also a folder (`docs/screens/{NAME}/index.md`), so relative paths to features use `../../features/`.
- `[@featureName](path)` syntax links to feature specs — path always ends with `/index.md`
- **Screen은 leaf만 참조한다.** branch(자식 폴더가 있는 피쳐)를 직접 가리키면 안 된다. 항상 leaf(자식 폴더가 없는 피쳐)의 `index.md`를 참조해야 한다.
- Every screen must reference at least one feature
- One feature can appear in multiple screens
- **Fixed areas** (lines 1, 5): Areas without `[@feature]` links describe fixed UI chrome (menu bar, status bar). These render as plain HTML without `data-feature` and are not included in FlowFrame metadata.
- **Multi-feature regions** (line 4): Multiple `[@feature]` references in one region — each gets its own `data-feature` container, stacked sequentially.
- **User stories**: 유저스토리는 feature leaf에 작성한다 (`docs/features/{name}/index.md`). Screen spec (`docs/screens/{NAME}/index.md`)에는 작성하지 않는다.
- **Acceptance criteria**: 기능 자체의 인수조건은 feature leaf에 작성한다. Screen spec (`docs/screens/{NAME}/index.md`)에는 작성하지 않는다.
- **Cross-feature AC**: Interactions between features on the same screen (e.g., file tree click → editor loads file). These are the **only** acceptance criteria in screen specs, separated into `## 화면 연계 인수조건`.

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
```

If the screen is effectively one feature, keep the screen spec this short instead of splitting it into micro-regions.

## Multi-viewport example

```markdown
---
screenId: LOGIN
title: 로그인
purpose: 사용자가 서비스에 로그인하는 화면
viewport: [pc, mobile]
---

# 로그인

## 레이아웃 (PC)

1. 좌: 브랜딩 이미지 | 우: 인증 폼 — [@auth/login-form](../../features/auth/login-form/index.md)

## 레이아웃 (Mobile)

1. 로고
2. 인증 폼 — [@auth/login-form](../../features/auth/login-form/index.md)
3. 소셜 로그인 — [@auth/social-login](../../features/auth/social-login/index.md)
```

Planners specify order + direction only. Visual details are the designer's domain.
Multi-viewport screen specs generate **separate HTML files** per viewport. Use a file naming rule that stays consistent within the project instead of assuming one fixed suffix pattern.
