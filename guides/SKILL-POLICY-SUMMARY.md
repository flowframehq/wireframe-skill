# Skill Policy Summary

이 문서는 `skills/flowframe-spec` 과 `skills/flowframe-wireframe` 에 흩어져 있는 핵심 정책을 한 곳에 모아 정리한 정책 문서다.
목적은 스킬 사용 중 판단 기준을 흔들리지 않게 유지하는 것이다.

---

## 1. 프로젝트 구조 정책

- 사용자 프로젝트의 기준 구조는 `docs/features/`, `docs/screens/`, `docs/flows/` 이다.
- wireframe HTML 은 각 screen 폴더 안에 둔다. 예: `docs/screens/LOGIN/wireframe.html`
- `feature` 는 비즈니스 기능 단위의 **폴더**다. 각 폴더에는 `index.md` 가 있다. 단독 `.md` 파일(예: `auth.md`)은 허용하지 않는다.
- `screen` 은 화면 단위의 **폴더**다. 단독 `.md` 파일(예: `CART.md`)이 아니다. 모든 screen 폴더에는 `index.md` 가 있어야 한다.
- `screen` 은 레이아웃과 feature 참조를 담는다.
- `flow` 는 여러 화면에 걸친 사용자 흐름과 인수조건을 담는 문서다.
- `wireframe` 은 FlowFrame 업로드용 단일 HTML 산출물이다.

---

## 2. Feature 정책

- feature 는 **폴더 단위**다. 단독 `.md` 파일이 아니다.
- 모든 feature 폴더에는 `index.md` 가 있어야 한다.
- `index.md` 외에 다른 `.md` 파일을 폴더 안에 두지 않는다.
- feature 는 재귀 구조를 가진다. `elements`, 하위 `features`, 또는 둘 다 함께 가질 수 있다.
- 자식 폴더가 있는 상위 feature 도 screen 에서 직접 참조할 수 있다.
- 상위 feature 의 `index.md` 도 필요하면 `## 와이어프레임 요소` 와 `usedIn` 을 가질 수 있다.
- feature 는 UI 요소 단위가 아니라 비즈니스 기능 단위로 나눈다.
- feature 는 독립 설명 가능해야 한다.
- feature 는 여러 화면에서 재사용 가능해야 한다.
- feature 는 한 명 또는 한 팀이 맡을 수 있는 개발 단위여야 한다.
- 로그인 화면처럼 단순한 화면도 screen 과 feature 를 분리한다.
- 로고, 구분선, 배경 이미지처럼 장식 전용 요소는 feature 로 만들지 않는다.
- feature 폴더명은 영어 kebab-case 로 작성한다.
- `featureId` 는 feature 폴더 경로에서 파생한 값이다. frontmatter 에 직접 쓰지 않는다.
- `usedIn` 은 screen 에서 직접 참조되는 feature 에 둘 수 있으며, 실제 `docs/screens/*/index.md` 목록과 항상 일치해야 한다.
- feature `index.md` 에는 `## 유저스토리` 와 `## 인수조건` 을 포함하지 않는다.

---

## 3. Screen 정책

- screen 은 **폴더 단위**다. 단독 `.md` 파일이 아니다. `docs/screens/{SCREEN_NAME}/index.md` 구조를 사용한다.
- 모든 screen 폴더에는 `index.md` 가 있어야 한다.
- `notes.md` 는 선택 사항으로, 기획자가 사전에 요구사항을 자유형식으로 적어두는 문서다.
- 스킬은 feature 작성 시 해당 screen 의 `notes.md` 를 먼저 읽고 반영한다. `notes.md` 가 있으면 이미 답이 있는 내용은 질문하지 않는다.
- `requirements.md` 는 screen 단위 유저스토리와 인수조건의 canonical 문서다.
- screen 은 레이아웃과 feature 참조를 담는다.
- screen 은 레이아웃과 화면 연계 인수조건의 기본 단위 문서다.
- screen 안에 feature 상세 구현 명세를 중복 작성하지 않는다.
- 모든 screen 은 최소 1개 이상의 feature 를 참조해야 한다.
- **screen 은 렌더링 가능한 feature 를 참조한다.** `elements` 도 없고 하위 `features` 도 없는 feature 참조는 금지한다.
- feature 참조 경로는 폴더 구조를 따른다. screen 이 폴더 안이므로 `../../` 로 상대 경로를 사용한다. 예: `[@auth/login-form](../../features/auth/login-form/index.md)`
- 화면이 사실상 하나의 feature 라면 screen 문서는 짧게 유지한다.
- screen 폴더명은 `LOGIN`, `PRODUCT_LIST` 같은 대문자 기반 이름을 사용한다.
- `screenId` 는 프로젝트에서 일관된 대문자 규칙을 유지한다.
- `viewport` 는 필수로 지정한다.
- 사용자가 선택할 수 있는 viewport 옵션은 `PC / 모바일 / 둘 다` 이다.
- `둘 다` 인 경우 한 screen 문서 안에서 `## 레이아웃 (PC)` 와 `## 레이아웃 (Mobile)` 로 분기한다.
- screen 문서에는 `## 유저스토리` 와 `## 인수조건` 을 작성하지 않는다. 이들은 `requirements.md` 에 작성한다.
- `requirements.md` 에는 같은 화면 안 책임 그룹별 `## 유저스토리` 와 `## 인수조건` 을 작성한다.
- `notes.md` 는 자유형식 메모이고, `requirements.md` 는 구조화된 검증 문서다.
- 같은 screen 에서 동일 feature 를 두 번 이상 참조하지 않는다.
- 동일 feature 가 화면의 여러 위치에 필요하면 feature 를 분리한다.
- 기획자는 정보 배치 순서와 방향만 정의한다.
- 간격, 비율, 시각적 디테일은 screen 정책이 아니라 디자인 영역이다.

---

## 3-1. Flow 정책

- 여러 화면을 가로지르는 사용자 흐름은 `docs/flows/*.md` 로 분리한다.
- 단일 화면 안에서 닫히는 상호작용은 flow 가 아니라 screen 문서에 남긴다.
- flow 문서는 `flowId`, `title`, `screens` frontmatter 를 가진다.
- flow 문서는 `## 유저스토리` 와 `## 인수조건` 을 포함한다.
- flow 파일은 영어 kebab-case 파일명으로 작성한다.

---

## 4. Wireframe 생성 정책

- wireframe 생성은 `docs/screens/*/index.md` 와 `docs/features/**/index.md` 를 기반으로 한다.
- output 은 각 screen 폴더 안에 만든다. 예: `docs/screens/LOGIN/wireframe.html`
- HTML 은 단일 파일이어야 한다.
- 스타일링은 Tailwind CSS v4 유틸리티 클래스로만 처리한다.
- 커스텀 CSS 나 인라인 스타일에 의존하지 않는다.
- 컬러 팔레트는 `zinc` 중심의 그레이스케일을 사용한다.
- 모든 색상 관련 클래스에 `dark:` 변형을 포함한다.
- PC 와 mobile 은 별도 HTML 파일로 생성한다.
- 단일 viewport 는 `wireframe.html` 을 사용한다.
- `pc` 와 `mobile` 동시 지원 시 `wireframe-pc.html`, `wireframe-mobile.html` 을 사용한다.

---

## 5. Metadata 정책

- 모든 wireframe HTML 은 `<script id="flowframe-meta" type="application/json">` 블록을 포함해야 한다.
- 필수 메타데이터 필드는 `generator`, `version`, `screenId`, `title`, `purpose`, `features` 이다.
- `generator` 값은 반드시 `"flowframe-wireframe-skill"` 이어야 한다.
- `viewport` 와 `author` 는 선택 필드다.
- `features` 는 1개 이상이어야 한다.
- 각 feature 엔트리는 `featureId`, `label`, `spec` 를 가지며, `elements` 또는 하위 `features` 중 최소 하나를 가져야 한다.
- 각 tracked element 는 `id`, `type`, `label`, `description` 을 가진다.
- `featureId` 는 feature 폴더 경로에서 파생한 값이다. frontmatter 에 직접 쓰지 않는다.
- 경로 파생 규칙: `docs/features/` 이후 경로에서 `index.md` 를 제거하고, 각 폴더명을 kebab-case → UPPER_SNAKE_CASE 로 변환하며, 깊이 구분자로 더블 언더스코어(`__`) 를 사용한다.
- 예: `docs/features/auth/login-form/index.md` → `AUTH__LOGIN_FORM`

---

## 6. 추적 ID 정책

- `data-feature` 는 metadata `elements[].id` 와 매칭되어야 한다.
- ID 형식은 `FEATURE_{FEATURE_ID}_{ELEMENT_ID}` 를 사용한다.
- `FEATURE_ID` 는 경로 파생 값이므로 깊이가 있을 때 `__` 를 포함한다.
- 예: `FEATURE_AUTH__LOGIN_FORM_EMAIL`, `FEATURE_AUTH__LOGIN_FORM_SUBMIT`, `FEATURE_CART_ITEMS_SELECT_ALL`
- 같은 화면 안에서 각 `elements[].id` 는 유일해야 한다.
- 하나의 feature 안에 여러 tracked element 가 있을 수 있다.
- 이 경우 `featureId` 와 `spec` 은 같고, `ELEMENT_ID` 만 달라진다.
- 동일 `elements[].id` 가 여러 DOM 노드에 나타날 수 있는데, 이는 같은 추적 요소의 상호배타 상태를 표현할 때만 허용한다.
- `ELEMENT_ID` 는 의미 중심의 영어 대문자 식별자를 사용한다.
- `BUTTON`, `TEXT`, `INPUT` 같은 일반 타입명 남용보다 역할 중심 이름을 우선한다.

---

## 7. `data-feature-id` / `data-feature` 정책

- 모든 feature 래퍼에는 `data-feature-id="{featureId}"` 와 `data-feature-label="{label}"` 을 부여한다.
- `data-feature` 는 **개별 추적 요소**(가장 작은 의미 단위의 UI 요소)에만 부여한다. 레이아웃용 wrapper div 에는 부여하지 않는다.
- 하나의 feature 가 여러 추적 요소를 가지면, feature 래퍼 안의 개별 요소 각각에 `data-feature` 가 생긴다.
- 메타데이터에 등록된 모든 요소는 HTML 안에서 대응하는 `data-feature` 를 가져야 한다.
- 반대로 HTML 의 tracked element 는 메타데이터에도 반드시 있어야 한다.
- 하위 feature 가 있으면 상위 `data-feature-id` 래퍼 안에 재귀적으로 중첩된다.
- 고정 UI chrome 은 `data-feature-id`, `data-feature` 둘 다 부여하지 않는다.
- feature 없는 고정 영역은 metadata 에 넣지 않는다.

---

## 8. 고정 영역 정책

- 상단 메뉴바, 하단 상태바, 헤더, 푸터처럼 feature 참조가 없는 영역은 고정 영역으로 본다.
- 고정 영역은 일반 HTML 로 렌더링한다.
- 고정 영역에는 `data-feature` 를 달지 않는다.
- 고정 영역은 FlowFrame 하이라이트 대상이 아니다.

---

## 9. 생성 방식 정책

- 최초 생성은 2-pass 방식으로 진행한다.
- Pass 1 에서는 screen 을 읽고 레이아웃 골격과 고정 영역을 만든다.
- Pass 2 에서는 screen 에서 참조한 feature 를 레이아웃 순서대로 읽고 해당 위치를 채운다.
- 모든 feature 를 한 번에 읽고 한 번에 생성하는 방식은 피한다.
- feature 에 `elements` 가 있으면 렌더링하고, 하위 `features` 가 있으면 재귀적으로 내려간다.
- `## 와이어프레임 요소` 가 비어 있거나 없더라도 하위 `features` 가 있으면 재귀 탐색을 계속한다.
- `## 와이어프레임 요소` 도 없고 하위 `features` 도 없으면 생성 중단 후 사용자에게 알려야 한다.

---

## 10. 업데이트 정책

- wireframe 업데이트는 자동 동기화가 아니라 사용자 명시 요청으로만 수행한다.
- 사용자가 수정한 feature 를 말하면, 에이전트는 해당 feature 의 `usedIn` 을 보고 영향 범위를 계산한다.
- 에이전트는 어떤 wireframe 이 영향받는지 먼저 사용자에게 보여줘야 한다.
- 사용자 확인 없이 바로 업데이트하지 않는다.
- 사용자는 일부 화면만 제외할 수 있다.
- `partial-update` 는 기존 레이아웃과 기존 feature 구조가 유지된 상태에서 요소 내용만 바뀐 경우다.
- `screen-regenerate` 는 화면 레이아웃 변경, feature 추가/제거, feature 계층 구조 변경인 경우다.
- `full-regenerate` 는 여러 화면에 걸친 구조 리팩터링인 경우다.
- 부분 업데이트 시에는 변경된 feature 의 `## 와이어프레임 요소`, 필요 시 `## 상태`, `## 인터랙션`, `requirements:` 포인터만 읽는다.
- 관련 없는 다른 feature 나 screen 을 다시 읽지 않는 것을 원칙으로 한다.
- 새 요소가 추가된 경우에는 해당 feature 영역과 metadata `features[].elements` 를 함께 갱신한다.

---

## 11. 삭제 정책

- feature 를 screen 에서 제거하면 `usedIn` 도 함께 정리해야 한다.
- feature 전체 삭제 시, 먼저 참조 중인 screen 목록을 확인해야 한다.
- 참조가 남아 있으면 사용자에게 경고하고 screen 쪽 참조 제거를 먼저 반영해야 한다.
- screen 삭제 시, 그 screen 이 참조하던 feature 들의 `usedIn` 에서 해당 screen 을 제거해야 한다.
- screen 삭제 후에는 해당 screen 폴더 안의 대응 HTML 삭제 여부도 사용자에게 확인해야 한다.

---

## 12. 정합성 정책

- screen 의 feature 참조와 실제 feature 폴더 (및 `index.md`) 존재 여부는 항상 맞아야 한다.
- feature 의 `usedIn` 과 실제 screen 참조 관계는 항상 맞아야 한다.
- wireframe 의 `data-feature-id`, `data-feature` 와 metadata `features` / `elements` 는 항상 맞아야 한다.
- metadata 의 `featureId`, `label`, `spec` 은 실제 feature 문서와 연결되어야 한다.
- screen 이 참조하는 핵심 feature 는 해당 screen 의 `requirements.md` 책임 그룹과 연결되어야 한다.
- 여러 화면에 걸친 유저스토리가 있으면 해당 흐름이 flow 문서에 반영되어야 한다.
- **feature frontmatter 에 `featureId` 가 없어야 한다.** featureId 는 경로에서 파생한다.
- **screen 이 참조하는 feature 가 렌더링 가능한지 확인해야 한다.** `elements` 도 없고 하위 `features` 도 없으면 정합성 위반이다.
- **wireframe 의 `featureId` 가 feature 경로 파생 값과 일치해야 한다.**

---

## 13. 재귀 feature 정책

- 상위 feature 는 하위 `features` 를 가질 수 있다.
- feature 는 `elements`, 하위 `features`, 또는 둘 다 함께 가질 수 있다.
- 상위 feature 에 `elements` 가 없더라도 하위 `features` 가 있으면 정상이다.
- wireframe 스킬은 화면에서 참조된 feature 로부터 시작해 하위 `features` 를 재귀적으로 읽는다.
- 상위 feature 와 더 구체적인 하위 feature 사이에 충돌이 있으면, 하위 feature 의 구체 규칙을 우선한다.

---

## 14. 유저스토리 및 인수조건 배치 정책

- screen 단위 유저스토리와 인수조건은 `docs/screens/{SCREEN_NAME}/requirements.md` 에 작성한다.
- screen `index.md` 에는 `## 유저스토리` 와 `## 인수조건` 을 작성하지 않는다.
- feature `index.md` 에는 유저스토리와 인수조건을 복제하지 않는다.
- 여러 화면에 걸친 유저스토리와 인수조건은 flow md 에 작성한다.
- 같은 화면 안의 여러 책임 그룹은 `requirements.md` 안에서 그룹별 heading 으로 나눈다.

---

## 15. 디자인 정책

- 와이어프레임의 목적은 예쁜 UI 가 아니라 구조 검토다.
- 메인 콘텐츠가 시각적 중심이 되어야 한다.
- 색보다 타이포와 간격으로 위계를 만든다.
- 장식 요소, 강한 그림자, 과한 배경 효과는 피한다.
- 버튼, 입력 필드, 카드, 패널은 단순한 박스 형태를 유지한다.
- 콘텐츠 밀도는 유지하되 불필요한 큰 빈 공간은 피한다.

---

## 16. 언어 정책

- 사용자에게 보여주는 선택지, 안내문, 확인문은 한국어를 우선한다.
- 예: `PC / 모바일 / 둘 다`, `다음 와이어프레임이 영향 받습니다.`
- 스킬 내부 설명 문장은 한 언어 톤을 유지해야 한다.
- 영어 설명 문장에서 한국어 섹션명을 쓸 때는 일반 명사처럼 섞지 말고, 리터럴 라벨로 다룬다.
- 예: `the \`## 와이어프레임 요소\` section`

---

## 17. 판단 원칙

- 확정되지 않은 파일명 규칙은 고정 정책처럼 쓰지 않는다.
- 사용자-facing 규칙과 agent-facing 설명 문장을 구분한다.
- 새 규칙을 만들기보다 기존 `docs/` 와 `skills/` 의 공통 정책을 우선 정렬한다.
- 모호할 때는 자동화보다 사용자 확인을 우선한다.
