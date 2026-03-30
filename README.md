# FlowFrame Wireframe Skill

기획자를 위한 AI 에이전트 스킬 패키지. 기능명세와 화면명세를 작성하고, FlowFrame에 업로드 가능한 HTML 와이어프레임을 생성합니다.

## 포함된 스킬

| 스킬 | 역할 |
|------|------|
| **flowframe-spec** | 기능명세(`docs/features/*/index.md`)와 화면명세(`docs/screens/*/index.md`) 작성 도우미 |
| **flowframe-wireframe** | 명세를 읽고 FlowFrame 규격 HTML 와이어프레임 생성/업데이트 |

## 설치

```bash
npx skills add flowframehq/wireframe-skill
```

## 사용법

### 1. 기능명세 작성

```
댓글 기능 기획해줘. 작성, 수정, 삭제, 멘션 기능이 필요해
```

→ `docs/features/comments/index.md` 생성 (와이어프레임 요소, 상태, 인터랙션, 비즈니스 로직, API)

### 2. 화면명세 작성

```
에디터 화면 만들어줘. 댓글이랑 버전관리 기능이 들어가
```

→ `docs/screens/EDITOR/index.md` 생성 (레이아웃 + 기능 참조)

### 3. 와이어프레임 생성

```
와이어프레임 만들어줘
```

→ `docs/screens/EDITOR/wireframe.html` 생성 (FlowFrame 업로드 가능)

### 4. 와이어프레임 업데이트

```
댓글이랑 인증 수정했어, 와이어프레임 업데이트해줘
```

→ 영향 받는 와이어프레임 목록 표시 → 사용자 컨펌 → 해당 HTML만 업데이트

## 프로젝트 구조

```
project/
└── docs/
    ├── features/          ← 기능 단위 명세 (폴더 기반, 재귀 구조)
    │   ├── auth/
    │   │   ├── index.md       ← branch (공통 컨텍스트)
    │   │   ├── login-form/
    │   │   │   └── index.md   ← leaf (와이어프레임 요소)
    │   │   └── social-login/
    │   │       └── index.md   ← leaf
    │   ├── comments/
    │   │   └── index.md       ← leaf (단독 기능)
    │   └── file-upload/
    │       └── index.md       ← leaf
    ├── screens/           ← 화면 정의 + 화면별 와이어프레임
    │   ├── LOGIN/
    │   │   ├── index.md
    │   │   └── wireframe.html
    │   ├── DASHBOARD/
    │   │   ├── index.md
    │   │   └── wireframe.html
    │   └── EDITOR/
    │       ├── index.md
    │       ├── wireframe-pc.html
    │       └── wireframe-mobile.html
    └── flows/             ← 화면 간 사용자 흐름과 인수조건
```

## 특징

- **기능 재사용** — 하나의 기능명세를 여러 화면에서 참조
- **부분 업데이트** — 수정된 기능의 와이어프레임 영역만 교체 (전체 재생성 불필요)
- **협업 지원** — 와이어프레임은 구조만, 상세 명세는 기능 md에서 확인
- **FlowFrame 호환** — 업로드 시 양방향 호버 하이라이트, 기능별 코멘트 가능

## 지원 에이전트

Claude Code, Cursor, GitHub Copilot, Windsurf, OpenCode, Codex, Cline, Gemini CLI 외 40+ 에이전트 자동 지원.

## 라이선스

MIT
