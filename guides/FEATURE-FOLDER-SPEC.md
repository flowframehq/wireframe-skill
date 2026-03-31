# Feature Folder Spec

> Feature의 재귀적 폴더 구조 명세.
> 기획 단위(비즈니스 기능)와 렌더링 단위(와이어프레임 요소)를 분리하여,
> 기능이 무한히 커져도 비즈니스 경계를 깨지 않고 구조적으로 확장할 수 있게 한다.

---

## 1. 핵심 원칙

| # | 원칙 | 설명 |
|---|------|------|
| 1 | **featureId는 경로 파생** | frontmatter에 featureId를 쓰지 않는다. 폴더 경로가 곧 ID다 |
| 2 | **screen은 렌더링 가능한 feature를 참조** | screen md는 `elements`가 있거나 하위 `features`를 가진 feature를 가리켜야 한다 |
| 3 | **feature는 재귀 구조를 가진다** | feature는 `elements`, 하위 `features`, 또는 둘 다 함께 가질 수 있다 |

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

## 3. 재귀 feature 구조

자식 폴더 유무로 depth 는 결정되지만, 렌더링 가능 여부는 `elements` 와 하위 `features` 보유 여부로 판단한다.

| 유형 | 판별 기준 | `## 와이어프레임 요소` | `usedIn` |
|------|----------|----------------------|----------|
| **독립 feature** | 하위 폴더 없음 | 있을 수 있음 | 보통 있음 |
| **상위 feature** | 하위 폴더 있음 | 있을 수도 있고 없을 수도 있음 | 있을 수도 있고 없을 수도 있음 |

- `elements`가 있으면 이 레벨 자체가 렌더링 대상이다
- 하위 `features`가 있으면 자식으로 재귀 탐색한다
- 둘 다 있으면 자체 UI와 하위 feature를 함께 표현할 수 있다
- 둘 다 없으면 불완전한 명세다

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

## 5. feature index.md 템플릿

렌더링 가능한 feature는 `## 와이어프레임 요소`를 가진다. 이 섹션이 있으면 와이어프레임 스킬이 해당 레벨을 렌더링한다.

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

## 비즈니스 로직

- 이메일 형식 검증
- 5회 실패 시 계정 잠금

requirements: ../../screens/LOGIN/requirements.md#인증
```

### frontmatter 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 (한국어) |
| `type` | Yes | `section`, `modal`, `drawer`, `dialog` 등 |
| `usedIn` | No | 이 feature를 직접 참조하는 screen md 경로 목록. 직접 참조되지 않으면 생략 가능 |

**`featureId` 필드는 없다.** 경로에서 파생한다.

---

## 6. 상위 feature index.md 템플릿

상위 feature 는 하위 `features` 를 가진다. 필요하면 자체 `elements` 와 `usedIn` 을 함께 가질 수 있다. `elements` 가 없더라도 하위 `features` 가 있으면 와이어프레임 스킬은 재귀 탐색을 계속한다.

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

### frontmatter 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `label` | Yes | 기능 이름 (한국어) |
| `type` | No | `section`, `modal`, `drawer`, `dialog` 등 |
| `usedIn` | No | 이 feature를 직접 참조하는 screen md 경로 목록 |

**`featureId` 필드는 없다.**

유저스토리와 인수조건은 feature 파일이 아니라 screen 폴더의 `requirements.md`에 작성한다.

### 공통 섹션 규칙

| 섹션 | 이름 | 용도 |
|------|------|------|
| `## 공통 상태` | 하위 피쳐 전체에 적용되는 상태 | 기획자/개발자 참고 |
| `## 공통 인터랙션` | 하위 피쳐 전체에 적용되는 인터랙션 | 기획자/개발자 참고 |
| `## 공통 비즈니스 로직` | 하위 피쳐 전체에 적용되는 비즈니스 규칙 | 기획자/개발자 참고 |

### 읽는 주체와 병합 규칙

| 주체 | 상위 feature를 읽는가 | 규칙 |
|------|---------------------|------|
| **wireframe 스킬** | **읽음** | 화면에서 참조된 feature부터 시작해 하위 `features`를 재귀 탐색한다. `elements`가 있으면 렌더링한다 |
| **spec/planner/developer** | **읽음** | 하위 feature를 읽을 때 상위 feature의 공통 섹션을 함께 참고한다 |

병합 시 충돌이 있으면 더 구체적인 하위 feature 규칙을 우선한다.

---

## 7. screen 폴더 구조

screen도 feature와 동일하게 **폴더 단위**로 관리한다.

```
docs/screens/
  {SCREEN_NAME}/
    index.md              ← 화면명세 (레이아웃 + 화면 연계 AC)
    notes.md              ← 선택. 기획자가 미리 적어둔 요구사항 메모
    requirements.md       ← screen 유저스토리 + 인수조건
```

규칙:

- 모든 screen은 **폴더**다. 단독 `.md` 파일(예: `CART.md`)은 허용하지 않는다
- 모든 폴더에는 반드시 `index.md`가 있다
- 폴더 이름은 **대문자** (기존 screenId 규칙과 동일: `CART`, `LOGIN`, `PRODUCT_LIST`)
- `notes.md`는 선택 사항이다. 없어도 된다
- `requirements.md`는 screen 유저스토리/인수조건 문서로 사용한다

### notes.md

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
- 스킬은 feature 작성 시 해당 screen의 `notes.md`를 **먼저 읽고** 반영한다
- `notes.md`가 있으면 이미 답이 있는 내용은 질문하지 않는다
- `notes.md`가 없으면 기존대로 질문한다

### screen index.md 참조

screen은 항상 **렌더링 가능한 feature**를 가리켜야 한다. `elements`도 없고 하위 `features`도 없는 feature 참조는 금지다.

```markdown
## 레이아웃 (PC)

1. 좌: 브랜딩 이미지
2. 우 상단 — [@auth/login-form](../../features/auth/login-form/index.md)
3. 우 하단 — [@auth/social-login](../../features/auth/social-login/index.md)
```

### 참조 표기

- `[@{경로}](../../features/{경로}/index.md)` — screen이 폴더 안이므로 `../../`로 상대 경로
- feature가 어느 깊이에 있든 동일한 방식

### 유저스토리/인수조건 배치

screen `index.md`에는 `## 유저스토리`와 `## 인수조건`을 쓰지 않는다. 이들은 screen 폴더의 `requirements.md`에 작성한다.

`requirements.md`는 같은 화면 안 책임 그룹별 유저스토리와 인수조건을 구조화해서 담는 canonical 문서다.

`notes.md`는 자유형식 메모이고, `requirements.md`는 structured verification 문서다.

### 금지 사항

```markdown
✓ [@auth](../../features/auth/index.md)                ← 상위 feature 참조. 허용.
✓ [@auth/login-form](../../features/auth/login-form/index.md)  ← 하위 feature 참조. 허용.

✗ 같은 feature를 한 화면에서 두 번 참조. 금지.
  동일 feature가 화면의 여러 위치에 필요하면 feature를 분리한다.
```

---

## 8. 와이어프레임 ID 매핑

featureId가 경로 파생이므로 와이어프레임 ID도 자동 결정된다.

### data-feature 패턴

```
FEATURE_{featureId}_{ELEMENT_ID}
```

### 예시

| feature 경로 | featureId | 요소 | data-feature |
|-----------|-----------|------|-------------|
| `auth/login-form/index.md` | `AUTH__LOGIN_FORM` | 이메일 입력 | `FEATURE_AUTH__LOGIN_FORM_EMAIL` |
| `auth/login-form/index.md` | `AUTH__LOGIN_FORM` | 로그인 버튼 | `FEATURE_AUTH__LOGIN_FORM_SUBMIT` |
| `cart-items/index.md` | `CART_ITEMS` | 전체 선택 | `FEATURE_CART_ITEMS_SELECT_ALL` |

### metadata feature / elements 예시

```json
{
  "featureId": "AUTH__LOGIN_FORM",
  "label": "로그인 폼",
  "spec": "../../features/auth/login-form/index.md",
  "elements": [
    {
      "id": "FEATURE_AUTH__LOGIN_FORM_EMAIL",
      "type": "input",
      "label": "이메일 입력",
      "description": "이메일 주소 입력 필드"
    }
  ]
}
```

- `featureId`: 경로 파생 값
- `spec`: feature의 `index.md`까지의 상대 경로
- `elements`: 이 feature에 속한 추적 요소 목록

---

## 9. 성장 시 확장 절차

기능이 커지면 기존 feature를 상위 feature로 유지하면서 하위 feature를 추가한다. 이때 기존 feature는 필요하면 자체 `elements` 와 `usedIn` 을 계속 유지할 수 있다.

### 승격 전

```
auth/
  index.md        ← 단일 feature. 와이어프레임 요소 13개. 너무 커짐.
```

### 승격 후

```
auth/
  index.md        ← 상위 feature로 확장. 공통 규칙 + 공용 요소 가능
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
3. 하위 feature 폴더를 생성하고 분리할 와이어프레임 요소를 이동한다
4. screen 이 상위 feature를 계속 참조할지, 하위 feature를 직접 참조할지 결정하고 `usedIn` 을 맞춘다
5. wireframe metadata와 `data-feature` ID가 실제 참조 구조와 일치하도록 갱신한다

---

## 10. 전체 예시

```
docs/features/
  cart-items/                          ← 단일 feature (작은 기능)
    index.md

  order-summary/                       ← 단일 feature (작은 기능)
    index.md

  auth/                                ← 상위 feature
    index.md                           ← 공통 상태, 공통 비즈니스 로직, 공용 요소 가능
    login-form/
      index.md                         ← 하위 feature
    social-login/
      index.md                         ← 하위 feature
    signup/
      index.md                         ← 하위 feature
    2fa/                               ← 상위 feature
      index.md                         ← 공통 비즈니스 로직
      totp/
        index.md                       ← 하위 feature
      sms/
        index.md                       ← 하위 feature

  product-management/                  ← 상위 feature
    index.md
    search/
      index.md                         ← 하위 feature
    table/                             ← 상위 feature
      index.md
      columns/
        index.md                       ← 하위 feature
      bulk-actions/
        index.md                       ← 하위 feature
    form/
      index.md                         ← 하위 feature
```

---

## 11. 정합성 규칙

| # | 규칙 | 검증 방법 |
|---|------|----------|
| 1 | 모든 feature 폴더에 `index.md`가 있어야 한다 | 폴더 스캔 |
| 2 | screen 이 직접 참조하는 feature 는 `elements` 또는 하위 `features` 중 하나 이상을 가져야 한다 | 파일 파싱 |
| 3 | 하위 폴더가 있는 상위 feature 도 `## 와이어프레임 요소`를 가질 수 있다 | 파일 파싱 |
| 4 | `usedIn`이 있는 feature 의 값과 실제 screen 참조가 일치해야 한다 | 양방향 크로스체크 |
| 5 | screen md는 렌더링 가능한 feature 만 참조해야 한다 | 참조 대상에 `elements` 또는 하위 `features`가 있는지 확인 |
| 6 | wireframe의 `featureId`가 feature 경로 파생 값과 일치해야 한다 | 경로 파싱 비교 |
| 7 | wireframe의 `spec`이 실제 feature `index.md`를 가리켜야 한다 | 경로 존재 확인 |
| 8 | feature frontmatter에 `featureId`가 **없어야** 한다 | 파일 파싱 |
| 9 | 모든 screen 폴더에 `index.md`가 있어야 한다 | 폴더 스캔 |
| 10 | screen md는 `## 유저스토리`와 `## 인수조건`을 포함하지 않아야 한다 | 파일 파싱 |
| 11 | 같은 screen에서 동일 feature를 두 번 이상 참조하지 않아야 한다 | screen md 파싱, 참조 중복 확인 |

---

## 12. 기존 구조와의 차이

| | 기존 (flat file) | 변경 후 (folder) |
|--|------------------|-----------------|
| feature 단위 | 파일 (`auth.md`) | 폴더 (`auth/index.md`) |
| screen 단위 | 파일 (`CART.md`) | 폴더 (`CART/index.md` + 선택적 `notes.md` + `requirements.md`) |
| featureId | frontmatter에 수기 작성 | 경로에서 자동 파생 |
| 렌더링 단위 | feature 전체 | 참조된 feature에서 시작하는 재귀 구조 |
| 비즈니스 단위 | feature 전체 | 폴더 전체 (깊이 무관) |
| 크기 제한 | 사실상 없음 | 필요 시 하위 feature로 확장 |
| 성장 대응 | feature 분할 (경계 파괴) | 폴더 내 하위 feature 추가 (경계 유지) |
| screen 참조 | `@feature` | `@feature`, `@feature/child` 모두 가능 |
| 상위 feature 역할 | 없음 | 공통 컨텍스트 + 공용 UI + 하위 feature 그룹화 |
| 요구사항 메모 | 없음 | screen 폴더의 `notes.md` (선택) |
| 유저스토리/인수조건 | feature | screen 폴더의 `requirements.md` |

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
4. 필요 시 `notes.md` 추가 (선택)
5. `requirements.md`에 screen 유저스토리/인수조건 작성
