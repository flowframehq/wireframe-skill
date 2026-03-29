# Feature Folder Spec

> Feature의 재귀적 폴더 구조 명세.
> 기획 단위(비즈니스 기능)와 렌더링 단위(와이어프레임 요소)를 분리하여,
> 기능이 무한히 커져도 비즈니스 경계를 깨지 않고 구조적으로 확장할 수 있게 한다.

---

## 1. 핵심 원칙

| # | 원칙 | 설명 |
|---|------|------|
| 1 | **featureId는 경로 파생** | frontmatter에 featureId를 쓰지 않는다. 폴더 경로가 곧 ID다 |
| 2 | **screen은 leaf만 참조** | screen md에서 branch를 참조하지 않는다. 항상 leaf를 직접 가리킨다 |
| 3 | **branch는 도메인 컨텍스트 전용** | branch에는 `## 와이어프레임 요소`가 없다. 렌더링에 참여하지 않는다 |

---

## 2. 폴더 구조

```
docs/features/
  {feature-name}/            ← 폴더 = 피쳐. kebab-case
    index.md                 ← 항상 존재. 이 피쳐의 내용
    {child-name}/            ← 선택. 하위 피쳐 (같은 구조)
      index.md
      {grandchild-name}/
        index.md
```

규칙:

- 모든 피쳐는 **폴더**다. 단독 `.md` 파일(예: `auth.md`)은 허용하지 않는다
- 모든 폴더에는 반드시 `index.md`가 있다
- 폴더 이름은 **kebab-case 영어**
- 깊이 제한 없음
- `index.md` 외에 다른 `.md` 파일을 폴더 안에 두지 않는다

---

## 3. leaf vs branch

자식 폴더 유무로 자동 결정된다. 수동 표기 없음.

| 유형 | 판별 기준 | `## 와이어프레임 요소` | `usedIn` |
|------|----------|----------------------|----------|
| **leaf** | 자식 폴더 없음 | **있음** — 렌더링 단위 | **있음** — 참조 화면 목록 |
| **branch** | 자식 폴더 있음 | **없음** — 자식에 위임 | **없음** — 직접 렌더링 안 됨 |

---

## 4. featureId 규칙

featureId는 frontmatter에 쓰지 않는다. 경로에서 자동 파생한다.

### 변환 규칙

1. `docs/features/` 이후의 경로를 취한다
2. `index.md`는 제거한다
3. 각 폴더명의 kebab-case를 UPPER_SNAKE_CASE로 변환한다
4. 깊이 구분자로 **더블 언더스코어(`__`)**를 사용한다

### 예시

| 경로 | featureId |
|------|-----------|
| `docs/features/cart-items/index.md` | `CART_ITEMS` |
| `docs/features/auth/index.md` | `AUTH` |
| `docs/features/auth/login-form/index.md` | `AUTH__LOGIN_FORM` |
| `docs/features/auth/2fa/index.md` | `AUTH__2FA` |
| `docs/features/auth/2fa/totp/index.md` | `AUTH__2FA__TOTP` |

- 단일 언더스코어(`_`)는 단어 연결용 (예: `CART_ITEMS`)
- 더블 언더스코어(`__`)는 깊이 구분용 (예: `AUTH__LOGIN_FORM`)
- 충돌 없음

### 스킬 파싱 로직

```
path.replace('docs/features/', '')
    .replace('/index.md', '')
    .split('/')
    .map(segment => segment.replace(/-/g, '_').toUpperCase())
    .join('__')
```

---

## 5. leaf index.md 템플릿

leaf는 렌더링 단위다. 와이어프레임 스킬이 읽는 대상.

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
| 이메일 입력 | input | 이메일 주소 입력 |
| 비밀번호 입력 | input | 비밀번호 입력 |
| 로그인 버튼 | button | 로그인 실행 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 빈 폼 |
| 에러 | 이메일/비밀번호 불일치 메시지 |

## 인터랙션

- 로그인 버튼 클릭 → 인증 요청

## 유저스토리

- 사용자로서 이메일과 비밀번호로 로그인하고 싶다, 내 계정에 접근하기 위해
- 사용자로서 로그인 실패 시 원인을 알고 싶다, 올바른 정보를 다시 입력하기 위해

## 인수조건

- Given 이메일과 비밀번호 입력 완료 When 로그인 버튼 클릭 Then 인증 요청 후 메인 화면 이동
- Given 이메일 형식 오류 When 로그인 버튼 클릭 Then 이메일 형식 에러 메시지 표시
- Given 5회 연속 로그인 실패 When 6번째 시도 Then 계정 잠금 안내 메시지 표시

## 비즈니스 로직

- 이메일 형식 검증
- 5회 실패 시 계정 잠금
```

### frontmatter 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 (한국어) |
| `type` | Yes | `section`, `modal`, `drawer`, `dialog` 등 |
| `usedIn` | Yes | 이 leaf를 참조하는 screen md 경로 목록 |

**`featureId` 필드는 없다.** 경로에서 파생한다.

---

## 6. branch index.md 템플릿

branch는 도메인 컨텍스트만 담는다. 와이어프레임 스킬은 branch를 읽지 않는다.

```markdown
---
label: 인증
---

# 인증

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

### frontmatter 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 (한국어) |

**`featureId`, `type`, `usedIn` 필드는 없다.**

**branch에는 `## 유저스토리`와 `## 인수조건`을 쓰지 않는다.** 유저스토리와 인수조건은 leaf에만 작성한다.

### 공통 섹션 규칙

| 섹션 | 이름 | 용도 |
|------|------|------|
| `## 공통 상태` | 하위 피쳐 전체에 적용되는 상태 | 기획자/개발자 참고 |
| `## 공통 인터랙션` | 하위 피쳐 전체에 적용되는 인터랙션 | 기획자/개발자 참고 |
| `## 공통 비즈니스 로직` | 하위 피쳐 전체에 적용되는 비즈니스 규칙 | 기획자/개발자 참고 |

### 읽는 주체와 병합 규칙

| 주체 | branch를 읽는가 | 규칙 |
|------|-----------------|------|
| **wireframe 스킬** | **읽지 않음** | leaf의 `## 와이어프레임 요소`만 읽는다 |
| **spec/planner/developer** | **읽음** | leaf를 읽을 때, ancestor branch의 공통 섹션을 함께 참고한다 |

병합 시 충돌이 있으면 **leaf 우선**. branch의 공통 규칙보다 leaf의 구체 규칙이 이긴다.

---

## 7. screen 폴더 구조

screen도 feature와 동일하게 **폴더 단위**로 관리한다.

```
docs/screens/
  {SCREEN_NAME}/
    index.md              ← 화면명세 (레이아웃 + 화면 연계 AC)
    requirements.md       ← 선택. 기획자가 미리 적어둔 요구사항
```

규칙:

- 모든 screen은 **폴더**다. 단독 `.md` 파일(예: `CART.md`)은 허용하지 않는다
- 모든 폴더에는 반드시 `index.md`가 있다
- 폴더 이름은 **대문자** (기존 screenId 규칙과 동일: `CART`, `LOGIN`, `PRODUCT_LIST`)
- `requirements.md`는 선택 사항이다. 없어도 된다

### requirements.md

기획자가 feature 작성 전에 미리 적어두는 **자유형식 요구사항 메모**.

```markdown
# 장바구니 요구사항

- 비회원도 장바구니 사용 가능
- 쿠폰은 1개만 적용 가능
- 품절 상품은 자동으로 선택 해제
- 50,000원 이상 무료배송
- 장바구니 최대 100개 상품
- 상품 옵션 변경은 상품 상세에서만 가능
```

- 형식 제한 없음. 기획자가 자유롭게 작성
- 스킬은 feature 작성 시 해당 screen의 `requirements.md`를 **먼저 읽고** 반영한다
- `requirements.md`가 있으면 이미 답이 있는 내용은 질문하지 않는다
- `requirements.md`가 없으면 기존대로 질문한다

### screen index.md 참조

screen은 항상 **leaf를 직접 가리킨다**. branch 참조는 금지.

```markdown
## 레이아웃 (PC)

1. 좌: 브랜딩 이미지
2. 우 상단 — [@auth/login-form](../../features/auth/login-form/index.md)
3. 우 하단 — [@auth/social-login](../../features/auth/social-login/index.md)
```

### 참조 표기

- `[@{경로}](../../features/{경로}/index.md)` — screen이 폴더 안이므로 `../../`로 상대 경로
- leaf가 어느 깊이에 있든 동일한 방식

### 유저스토리/인수조건 배치

screen md에는 `## 유저스토리`와 `## 인수조건`을 쓰지 않는다. 기능 단위의 유저스토리와 인수조건은 해당 feature leaf의 `index.md`에 작성한다. 같은 feature가 여러 화면에서 사용되더라도 유저스토리와 인수조건은 feature에 한 번만 작성한다.

screen md에는 `## 화면 연계 인수조건`만 작성한다. 화면 연계 인수조건은 같은 화면 안의 서로 다른 feature 간 상호작용을 검증하는 조건이다.

### 금지 사항

```markdown
✗ [@auth](../../features/auth/index.md)          ← branch 참조. 금지.
✓ [@auth/login-form](../../features/auth/login-form/index.md)  ← leaf 참조. 허용.

✗ 같은 leaf를 한 화면에서 두 번 참조. 금지.
  동일 feature가 화면의 여러 위치에 필요하면 leaf를 분리한다.
```

---

## 8. 와이어프레임 ID 매핑

featureId가 경로 파생이므로 와이어프레임 ID도 자동 결정된다.

### data-feature 패턴

```
FEATURE_{featureId}_{ELEMENT_ID}
```

### 예시

| leaf 경로 | featureId | 요소 | data-feature |
|-----------|-----------|------|-------------|
| `auth/login-form/index.md` | `AUTH__LOGIN_FORM` | 이메일 입력 | `FEATURE_AUTH__LOGIN_FORM_EMAIL` |
| `auth/login-form/index.md` | `AUTH__LOGIN_FORM` | 로그인 버튼 | `FEATURE_AUTH__LOGIN_FORM_SUBMIT` |
| `cart-items/index.md` | `CART_ITEMS` | 전체 선택 | `FEATURE_CART_ITEMS_SELECT_ALL` |

### metadata elements

```json
{
  "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
  "featureId": "AUTH__LOGIN_FORM",
  "type": "input",
  "label": "이메일 입력",
  "description": "이메일 주소 입력 필드",
  "spec": "../features/auth/login-form/index.md"
}
```

- `featureId`: 경로 파생 값
- `spec`: leaf의 `index.md`까지의 상대 경로

---

## 9. 성장 시 승격 절차

기능이 커지면 leaf를 branch로 승격한다.

### 승격 전

```
auth/
  index.md        ← leaf. 와이어프레임 요소 13개. 너무 커짐.
```

### 승격 후

```
auth/
  index.md        ← branch로 전환
  login-form/
    index.md      ← leaf. 기존 요소 중 로그인 관련
  social-login/
    index.md      ← leaf. 기존 요소 중 소셜 관련
  signup/
    index.md      ← leaf. 기존 요소 중 회원가입 관련
```

### 승격 체크리스트

1. 기존 leaf `index.md`에서 `## 와이어프레임 요소`와 `usedIn`을 제거 → branch로 전환
2. 기존 상태/인터랙션/비즈니스 로직 중 공통 부분은 branch에 남기고, 섹션 이름에 `공통` 접두어 부여
3. 하위 leaf 폴더를 생성하고 각각에 해당 와이어프레임 요소 이동
4. 각 leaf에 `usedIn` 설정
5. **screen md 참조 업데이트**: 기존 `@auth` → `@auth/login-form`, `@auth/social-login` 등으로 분할
6. **wireframe의 data-feature 업데이트**: 기존 `FEATURE_AUTH_*` → `FEATURE_AUTH__LOGIN_FORM_*` 등으로 변경

---

## 10. 전체 예시

```
docs/features/
  cart-items/                          ← leaf (작은 기능)
    index.md

  order-summary/                       ← leaf (작은 기능)
    index.md

  auth/                                ← branch
    index.md                           ← 공통 상태, 공통 비즈니스 로직
    login-form/
      index.md                         ← leaf
    social-login/
      index.md                         ← leaf
    signup/
      index.md                         ← leaf
    2fa/                               ← branch
      index.md                         ← 공통 비즈니스 로직
      totp/
        index.md                       ← leaf
      sms/
        index.md                       ← leaf

  product-management/                  ← branch
    index.md
    search/
      index.md                         ← leaf
    table/                             ← branch
      index.md
      columns/
        index.md                       ← leaf
      bulk-actions/
        index.md                       ← leaf
    form/
      index.md                         ← leaf
```

---

## 11. 정합성 규칙

| # | 규칙 | 검증 방법 |
|---|------|----------|
| 1 | 모든 feature 폴더에 `index.md`가 있어야 한다 | 폴더 스캔 |
| 2 | leaf의 `index.md`에는 `## 와이어프레임 요소`가 있어야 한다 | 파일 파싱 |
| 3 | branch의 `index.md`에는 `## 와이어프레임 요소`가 **없어야** 한다 | 파일 파싱 |
| 4 | leaf의 `usedIn`과 실제 screen 참조가 일치해야 한다 | 양방향 크로스체크 |
| 5 | screen md는 leaf만 참조해야 한다 (branch 참조 금지) | 참조 대상 폴더에 자식 폴더가 없는지 확인 |
| 6 | wireframe의 `featureId`가 leaf 경로 파생 값과 일치해야 한다 | 경로 파싱 비교 |
| 7 | wireframe의 `spec`이 leaf `index.md`를 가리켜야 한다 | 경로 존재 확인 |
| 8 | leaf의 frontmatter에 `featureId`가 **없어야** 한다 | 파일 파싱 |
| 9 | 모든 screen 폴더에 `index.md`가 있어야 한다 | 폴더 스캔 |
| 10 | screen md는 `## 유저스토리`와 `## 인수조건`을 포함하지 않아야 한다 | 파일 파싱 |
| 11 | 같은 screen에서 동일 leaf를 두 번 이상 참조하지 않아야 한다 | screen md 파싱, 참조 중복 확인 |

---

## 12. 기존 구조와의 차이

| | 기존 (flat file) | 변경 후 (folder) |
|--|------------------|-----------------|
| feature 단위 | 파일 (`auth.md`) | 폴더 (`auth/index.md`) |
| screen 단위 | 파일 (`CART.md`) | 폴더 (`CART/index.md` + 선택적 `requirements.md`) |
| featureId | frontmatter에 수기 작성 | 경로에서 자동 파생 |
| 렌더링 단위 | feature 전체 | leaf `index.md` |
| 비즈니스 단위 | feature 전체 | 폴더 전체 (깊이 무관) |
| 크기 제한 | 사실상 없음 | leaf는 항상 적정 크기 |
| 성장 대응 | feature 분할 (경계 파괴) | 폴더 내 승격 (경계 유지) |
| screen 참조 | `@feature` | `@feature/child` (leaf 직접 지정) |
| branch 역할 | 없음 | 공통 컨텍스트 전용, 렌더링 미참여 |
| 요구사항 | 없음 | screen 폴더의 `requirements.md` (선택) |

---

## 13. 마이그레이션 가이드

기존 flat file을 폴더 구조로 전환하는 절차:

### feature 마이그레이션

1. `docs/features/{name}.md` → `docs/features/{name}/index.md`로 이동
2. frontmatter에서 `featureId` 필드 삭제
3. 경로 파생 featureId가 기존 featureId와 일치하는지 확인
4. screen md의 참조 경로 업데이트: `../features/{name}.md` → `../../features/{name}/index.md`
5. wireframe의 `spec` 경로 업데이트
6. wireframe metadata의 `featureId`가 경로 파생 값과 일치하는지 확인

### screen 마이그레이션

1. `docs/screens/{NAME}.md` → `docs/screens/{NAME}/index.md`로 이동
2. feature 참조 경로의 상대 경로 업데이트: `../features/` → `../../features/`
3. feature의 `usedIn` 경로 업데이트: `docs/screens/{NAME}.md` → `docs/screens/{NAME}/index.md`
4. 필요 시 `requirements.md` 추가 (선택)
