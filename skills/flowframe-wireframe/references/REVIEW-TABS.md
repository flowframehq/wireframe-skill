# 리뷰 탭 패턴

하나의 feature가 같은 와이어프레임 안에서 2~4개의 상호배타 형제 상태를 보여줘야 할 때 사용한다.
리뷰 컨트롤이지, 제품 UI가 아니다.

## 사용 시점

- 빈 상태 vs 데이터 있는 상태
- 활성 vs 비활성 액션 세트
- 다음 조작 판단에 영향을 주는 성공 vs 실패 피드백
- 같은 feature 영역의 역할 기반 가용성

## 사용하지 않을 때

- 실제 제품 내 내비게이션
- 글로벌 페이지 탭
- 비교가 필요 없는 일회성 경고 텍스트
- 트리거 근처에 직접 렌더링해야 하는 다이얼로그/모달

## 계약

- 비교 대상 feature를 하나의 `feature-review` 컨테이너로 감싼다
- 실제 제품 UI는 상태 패널 안에 배치한다
- 리뷰 컨트롤은 제품 UI 흐름 밖에 absolute 오버레이로 배치한다
- 기본 상태에서 숨기고, feature hover/focus 시 드러낸다
- 한 번에 하나의 패널만 활성
- 비활성 패널은 `hidden`
- `VIEW` 같은 라벨을 추가하지 않는다
- 와이어프레임 도구처럼 스타일링한다, 제품 탭바처럼 하지 않는다
- Tailwind 유틸리티 클래스, 네이티브 HTML 컨트롤, 공유 `:has()` 스타일 블록을 사용한다
- 탭 동작에 JS를 사용하지 않는다
- 같은 `data-feature` 값의 중복은 같은 추적 요소의 상호배타 상태를 나타낼 때만 허용
- 기본 상태에서 최소 하나의 매칭 `data-feature` 인스턴스가 보여야 한다
- 라디오 `name`과 모든 input `id`는 feature 인스턴스별로 고유해야 한다
- Tailwind group 이름은 `{feature-key}`를 사용한다 (예: `group/login`, `group/order-list`) — `group/feature` 같은 고정 리터럴 금지. 고정 이름은 CSS `group-hover`가 중첩 래퍼를 통해 전파되어, 외부 래퍼 호버 시 모든 내부 리뷰 탭이 동시에 드러난다

## 활성 탭 스타일링

라벨이 `.review-tabs` 안에 중첩되어 있으므로 Tailwind `peer-checked`가 도달하지 못한다 (peer는 후속 형제에서만 작동). 활성 탭 스타일은 `<style type="text/tailwindcss">`의 공유 `:has()` CSS 블록으로 처리한다. N번째 체크된 라디오와 N번째 라벨을 위치 기반으로 매칭한다:

```css
/* 리뷰 탭: 활성 라벨 스타일 (위치 기반, 최대 4탭 지원) */
.feature-review:has(> input:nth-of-type(1):checked) .review-tabs label:nth-of-type(1),
.feature-review:has(> input:nth-of-type(2):checked) .review-tabs label:nth-of-type(2),
.feature-review:has(> input:nth-of-type(3):checked) .review-tabs label:nth-of-type(3),
.feature-review:has(> input:nth-of-type(4):checked) .review-tabs label:nth-of-type(4) {
  @apply bg-white text-zinc-900 font-semibold shadow-sm dark:bg-zinc-800 dark:text-zinc-100;
}
```

이 규칙은 페이지 `<style>` 블록에 **한 번만** 작성하면 모든 리뷰 탭 인스턴스에 적용된다. feature별로 복제하지 않는다.

## 재사용 레시피

```html
<!-- 공유 스타일 블록 (와이어프레임당 한 번, <style type="text/tailwindcss"> 안에) -->
<style type="text/tailwindcss">
  @custom-variant dark (&:where(.dark, .dark *));

  .feature-review:has(> input:nth-of-type(1):checked) .review-tabs label:nth-of-type(1),
  .feature-review:has(> input:nth-of-type(2):checked) .review-tabs label:nth-of-type(2),
  .feature-review:has(> input:nth-of-type(3):checked) .review-tabs label:nth-of-type(3),
  .feature-review:has(> input:nth-of-type(4):checked) .review-tabs label:nth-of-type(4) {
    @apply bg-white text-zinc-900 font-semibold shadow-sm dark:bg-zinc-800 dark:text-zinc-100;
  }
</style>

<!-- feature별 리뷰 탭 인스턴스 -->
<div data-feature-id="{FEATURE_ID}" data-feature-label="{label}" class="feature-review group/{feature-key} relative">
  <input class="peer/default sr-only" type="radio" name="{feature-key}-review" id="{feature-key}-default" checked />
  <input class="peer/empty sr-only" type="radio" name="{feature-key}-review" id="{feature-key}-empty" />

  <div class="review-tabs absolute -top-3 right-4 z-20 flex items-center gap-1 rounded-full border border-dashed border-zinc-300 dark:border-zinc-600 bg-zinc-50/95 dark:bg-zinc-900/95 px-1.5 py-1 shadow-sm opacity-0 pointer-events-none translate-y-1 transition-all group-hover/{feature-key}:opacity-100 group-hover/{feature-key}:pointer-events-auto group-hover/{feature-key}:translate-y-0 group-focus-within/{feature-key}:opacity-100 group-focus-within/{feature-key}:pointer-events-auto group-focus-within/{feature-key}:translate-y-0">
    <label for="{feature-key}-default" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">
      기본
    </label>
    <label for="{feature-key}-empty" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">
      결과 없음
    </label>
  </div>

  <div class="peer-checked/default:block hidden">
    <!-- 실제 feature UI -->
  </div>

  <div class="peer-checked/empty:block hidden">
    <!-- 대안 feature UI -->
  </div>
</div>
```

## 동작 원리

| 메커니즘 | 역할 | 작동 이유 |
|---------|------|----------|
| `peer-checked/{name}:block` (패널 div) | 어떤 패널이 보이는지 전환 | 패널이 라디오 input의 **형제**이므로 |
| `:has(> input:nth-of-type(N):checked)` (`.feature-review`) | N번째 라벨을 활성 스타일로 표시 | `:has()`는 **중첩된** 하위 요소까지 도달 가능 |
| `group-hover/{feature-key}` (`.review-tabs`) | 호버 시 오버레이 표시 | group 스코핑으로 중첩된 리뷰 탭이 독립 작동 |

## 스타일링 참고

- 기본 오버레이 위치는 `absolute -top-3 right-4`
- 점선 테두리와 작은 pill chip으로 도구 느낌
- 상태 패널 간 제품 간격과 레이아웃을 가능한 동일하게 유지
- 오버레이가 레이아웃 공간을 차지하거나 feature 콘텐츠를 밀어내지 않도록
- 드러남 동작은 `group-hover` / `group-focus-within` 사용, 커스텀 셀렉터가 아님
- `{feature-key}`를 고유하고 안정적인 슬러그로 교체한다 (예: `login`, `product-list`, `order-actions`) — 이 슬러그는 group 이름(`group/{feature-key}`), 라디오 name, input ID에 공통으로 사용한다. 세 곳 모두 같은 슬러그여야 hover 드러남이 올바른 래퍼에 스코핑된다
- 라벨에는 `peer-checked` 클래스가 **필요 없다** — 활성 스타일은 전적으로 공유 `:has()` 규칙이 처리
- `:has()` 규칙은 최대 4탭까지 지원한다. 2탭이나 3탭 인스턴스에서는 미사용 nth-of-type 규칙이 단순히 매칭되지 않는다

## 사용 예제

- [EXAMPLE.md](EXAMPLE.md)
- [PRODUCT-LIST-EXAMPLE.md](PRODUCT-LIST-EXAMPLE.md)
