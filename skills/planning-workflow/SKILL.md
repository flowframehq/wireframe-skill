---
name: planning-workflow
description: docs2/intake.md를 기준으로 V2 화면 기획 워크플로우를 오케스트레이션한다. 사용자가 "화면 기획 시작해", "docs2 기준으로 기획해", "v2 기획 하네스 시작해"처럼 말하며 planner-reviewer 게이트를 포함한 기획 흐름을 한 번에 실행하고 싶을 때 사용한다.
license: MIT
metadata:
  author: flowframehq
  version: "0.2.0"
---

# 화면 기획 워크플로우

이 스킬은 단일 intake 문서를 시작점으로 V2 기획 하네스를 실행한다.
역할은 세부 명세 작성이 아니라 기획 오케스트레이션이다.
즉, 단계 순서와 산출물, 리뷰 게이트를 통제하는 상위 워크플로우다.

이 스킬이 `flowframe-spec` 자체를 대체하는 것은 아니다.
V2 문서 트리에서 언제 어떤 순서로 기획 단계를 실행할지 결정하는 용도로 사용한다.

## 버전 규칙

이 워크플로우는 버전 2 기획 전용이다.

- 기존 `docs/` 트리는 그대로 둔다
- 입력 intake 는 `docs2/` 아래에서 읽는다
- V2 기획 산출물은 모두 `docs2/` 아래에 쓴다
- 사용자가 명시적으로 요청하기 전에는 `docs/` 아래 파일을 이동, 덮어쓰기, 삭제하지 않는다

## 사용 시점

아래 경우에 이 스킬을 사용한다.

- `docs2/intake.md` 기준으로 기획을 시작하고 싶을 때
- `docs2 기준으로 화면 기획 시작해`라고 요청할 때
- 수동으로 명세를 하나씩 쓰는 대신 V2 기획 하네스를 실행하고 싶을 때
- planner -> reviewer -> gate 흐름을 새 문서 버전에서 돌리고 싶을 때

아래 경우에는 이 스킬을 사용하지 않는다.

- 기존 feature 명세 하나만 수동 수정할 때
- 기존 screen 명세 하나만 수동 수정할 때
- 와이어프레임 생성만 원할 때

이런 경우에는 더 하위의 전용 스킬을 직접 사용한다.

## 기본 시작점

기본 입력 파일:

```text
docs2/intake.md
```

사용자가 `docs2/` 아래 다른 intake 파일 경로를 주면 그 경로를 사용한다.
intake 파일이 없으면 진행하지 말고 올바른 경로를 확인한다.

## 사용자 시작 명령

사용자는 메인 패널에서 의도 중심의 짧은 명령으로 이 워크플로우를 시작해야 한다.
권장 예시는 아래와 같다.

- `docs2/intake.md 기준으로 화면 기획 시작해`
- `docs2 기준 planning workflow 실행해`
- `v2 기획 하네스 시작해`

사용자에게 planner, reviewer, spec 작성 단계를 직접 나열해서 실행하게 요구하지 않는다.

## 워크플로우 책임

이 스킬은 아래를 알고 있어야 한다.

1. 어떤 V2 intake 파일을 읽을지
2. 어떤 순서로 기획 단계를 실행할지
3. V2 산출물을 어느 경로에 쓸지
4. 어떤 리뷰 게이트와 중단 조건을 적용할지

이 스킬은 열린 형태의 자율 에이전트처럼 동작하면 안 된다.
명시적인 산출물과 판정 기준을 가진 제한된 워크플로우처럼 동작해야 한다.

## V2 파일 규칙

V2 기획 트리는 아래 구조를 기준으로 본다.

```text
docs2/
  intake.md
  features/
  screens/
  flows/
```

이 워크플로우가 만드는 모든 산출물은 `docs2/` 아래에 둔다.
기존 `docs/` 트리는 이전 버전으로 유지한다.

## 필수 산출물

각 대상 screen 에 대해 아래 파일을 생성하거나 갱신한다.

- 자유형식 원문 메모를 남겨야 하면 `docs2/screens/{SCREEN_NAME}/notes.md`
- screen 유저스토리와 인수조건의 기준 문서로 `docs2/screens/{SCREEN_NAME}/requirements.md`
- 레이아웃과 feature 참조 문서로 `docs2/screens/{SCREEN_NAME}/index.md`

필요한 각 feature 에 대해 아래 파일을 생성하거나 갱신한다.

- `docs2/features/**/index.md`

intake 가 여러 화면이나 화면 간 흐름을 포함하면 아래도 생성하거나 갱신한다.

- `docs2/flows/*.md`

## 단계 모델

아래 고정 순서로 워크플로우를 실행한다.

### 1단계. Intake 정규화

`docs2/intake.md`를 읽고 아래 구조로 정리한다.

- 제품 또는 운영 목표
- 범위에 포함되는 화면
- 범위에서 제외되는 항목
- 사용자 역할
- 주요 작업
- 정책 또는 운영 제약
- 열린 질문

intake 가 자유형식 문서라면 먼저 짧은 구조화 요약으로 변환한다.
이 요약이 안정되기 전에는 spec 작성을 시작하지 않는다.

### 2단계. Screen 경계 도출

intake 에서 V2 screen 목록을 도출한다.
각 screen 후보는 아래 정보를 가져야 한다.

- screen 이름
- 목적
- 핵심 사용자 행동
- 관련 feature 책임 단위
- viewport 가정

intake 가 긴 하나의 흐름처럼 적혀 있더라도 feature 기획 전에 screen 단위로 먼저 나눈다.

### 3단계. 책임 단위 분해

각 screen 을 개발 책임 단위로 분해한다.
이 단계는 feature 생성 전에 반드시 수행한다.

다음 질문을 기준으로 분해한다.

1. 무엇이 독립적으로 구현될 수 있는가
2. 무엇이 하나의 책임으로 함께 남아야 하는가
3. 무엇이 상위 feature 가 되고 그 아래에 하위 feature 가 생겨야 하는가

보이는 UI 조각을 기준으로 feature 명세를 만들지 않는다.
비즈니스 책임 단위로 묶는다.

### 4단계. Spec 초안 작성

아래 V2 기획 산출물을 작성한다.

- `docs2/features/` 아래 재귀 feature 명세
- `docs2/screens/{SCREEN_NAME}/` 아래 screen `requirements.md`
- `docs2/screens/{SCREEN_NAME}/` 아래 screen `index.md`

규칙은 아래와 같다.

- screen 유저스토리와 인수조건은 `requirements.md`에 작성한다
- screen `index.md`는 레이아웃 중심으로 유지한다
- 원문 표현 중 유의미한 내용은 `notes.md`에 보존하되 기준 검증 문서로 취급하지 않는다

### 5단계. Review gate

기획 완료로 선언하기 전에 planning review 를 한 번 수행한다.
최소한 아래를 확인한다.

- 모든 screen 에 목적이 명확히 적혀 있는지
- 모든 screen 이 최소 1개의 렌더링 가능한 feature 를 참조하는지
- 모든 직접 참조 feature 가 경계가 분명한 책임을 갖는지
- `requirements.md`의 각 책임 그룹이 screen 레이아웃이나 feature 명세 또는 둘 다에 반영되는지
- 화면 간 흐름이 필요할 때 `docs2/flows/*.md`로 분리되었는지
- 인수조건이 테스트 가능하고 관찰 가능한지

### 6단계. 마무리

review 가 통과한 뒤에만 아래를 수행한다.

- 생성되거나 갱신된 V2 파일 보고
- 남은 리스크나 보류 질문 보고
- 다음 워크플로우 제안

review 가 통과하지 못하면 `needs_revision` 상태로 멈추고 막는 이슈를 설명한다.

## Review gate 상태값

아래 내부 상태값을 일관되게 사용한다.

- `drafting`
- `needs_revision`
- `passed`

게이트가 아직 `needs_revision` 이면 V2 산출물을 완료로 표시하지 않는다.

## 중단 조건

아래 경우에는 중단하고 문제를 드러낸다.

- V2 intake 파일이 없을 때
- intake 에서 그럴듯한 screen 경계를 하나도 도출할 수 없을 때
- 어떤 screen 이 렌더링 불가능한 feature 만 참조하게 될 때
- 현재 정보로는 인수조건을 테스트 가능하게 만들 수 없을 때
- 같은 책임이 여러 feature 에 이유 없이 중복될 때

부족한 동작을 추정해서 채우기보다, 정확한 gap report 와 함께 멈추는 쪽을 우선한다.

## 오케스트레이션 규칙

이 스킬은 V2 기획의 최상위 워크플로우 드라이버다.
하위 planning 동작을 재사용할 수 있지만, 단계 순서와 gate 조건의 통제권은 유지해야 한다.

실행 규칙은 아래와 같다.

- 사용자에게 하위 스킬을 수동 실행하게 시키지 않는다
- planning 이 통과하기 전에는 wireframe 생성으로 넘어가지 않는다
- 책임 단위 분해를 건너뛰지 않는다
- 원문 입력 문장을 최종 기준 명세로 취급하지 않는다
- V2 산출물을 `docs/` 아래에 쓰지 않는다

## 출력 요약 형식

워크플로우 종료 시 아래 형식으로 요약한다.

```text
기획 상태: 통과 | 수정 필요
입력: docs2/intake.md
기획된 화면:
- SCREEN_NAME: 목적

산출물:
- docs2/screens/SCREEN_NAME/index.md
- docs2/screens/SCREEN_NAME/requirements.md
- docs2/features/...

열린 이슈:
- ...

다음 권장 단계:
- docs2 기준 와이어프레임 생성
```

## 가드레일

- 입력 문서가 강하게 시사하지 않으면 백엔드 정책을 임의로 만들지 않는다
- intake 에 여러 사용자 목표가 명확하면 억지로 하나의 거대한 screen 으로 합치지 않는다
- feature 를 버튼 수준 하위 기능으로 과도하게 쪼개지 않는다
- 레이아웃에서 가까이 있다는 이유만으로 서로 다른 개발 책임을 하나의 feature 로 뭉치지 않는다
- review gate 를 우회하지 않는다
- 이 V2 workflow 실행 중에는 `docs/`를 수정하지 않는다

## 최소 실행 예시

입력:

```text
docs2/intake.md
```

사용자 명령:

```text
docs2/intake.md 기준으로 화면 기획 시작해
```

실행 순서:

1. V2 입력 문서를 읽고 정규화한다
2. screen 목록을 도출한다
3. screen 별 책임 단위를 분해한다
4. V2 feature 명세와 screen 문서를 작성한다
5. planning 완성도를 검토한다
6. `통과` 또는 `수정 필요`를 반환한다

## 핸드오프

이 워크플로우가 통과하면 다음 권장 단계는 `docs2/` 기준 V2 와이어프레임 생성이다.
핸드오프 기준은 아래와 같다.

- 완료된 `docs2/screens/*/index.md`
- 완료된 `docs2/screens/*/requirements.md`
- 참조된 V2 feature 명세

workflow 가 아직 `수정 필요` 이면 wireframe 생성으로 넘기지 않는다.
