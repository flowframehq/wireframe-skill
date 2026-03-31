# 예제: 상품 목록 화면 와이어프레임

**다중 feature 상품 목록 화면**을 현재 `flowframe-wireframe` 계약에 맞춰 구현한 레퍼런스.
필터 feature와 메인 목록 feature를 하나의 화면에 결합하면서 리뷰 탭으로 빈 상태를 노출하는 구조를 보여준다.
재사용 가능한 리뷰 탭 래퍼 자체는 [REVIEW-TABS.md](REVIEW-TABS.md)를 참조한다.

## 목차

- [1단계: 기능 명세](#1단계-기능-명세)
- [2단계: 화면 명세](#2단계-화면-명세)
- [3단계: 화면 요구사항](#3단계-화면-요구사항)
- [4단계: 생성된 와이어프레임](#4단계-생성된-와이어프레임)
- [핵심 패턴](#핵심-패턴)

## 1단계: 기능 명세

### docs/features/search-filter/index.md

```markdown
---
label: 검색/필터
type: section
usedIn:
  - docs/screens/PRODUCT_LIST/index.md
---

# 검색/필터

## 와이어프레임 요소

| 요소 | type | 설명 |
|------|------|------|
| 카테고리 필터 | list | 상품 카테고리 체크박스 목록 |
| 가격 범위 | input | 최소~최대 가격 입력 필드 |
| 필터 적용 버튼 | button | 선택한 조건을 목록에 반영하는 실행 버튼 |
| 필터 초기화 | link | 적용된 필터를 모두 해제하는 링크 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 모든 필터 비활성, 전체 목록 조회 상태 |
| 필터 적용 중 | 일부 카테고리 선택, 가격 범위 입력 완료 |

## 인터랙션

- 카테고리 체크 → 목록 조건 즉시 변경
- 가격 범위 입력 → 필터 적용 버튼 클릭 시 반영
- 필터 초기화 클릭 → 모든 필터 해제

requirements: ../../screens/PRODUCT_LIST/requirements.md#검색-및-필터
```

### docs/features/product-list/index.md

```markdown
---
label: 상품 목록
type: section
usedIn:
  - docs/screens/PRODUCT_LIST/index.md
---

# 상품 목록

## 와이어프레임 요소

| 요소 | type | 설명 |
|------|------|------|
| 정렬 선택 | select | 인기순, 최신순, 가격순 정렬 드롭다운 |
| 상품 카드 | list | 상품 이미지, 이름, 가격, 평점을 보여주는 카드 그리드 |
| 페이지네이션 | button | 이전/다음 페이지 이동 버튼 |

## 상태

| 상태 | 설명 |
|------|------|
| 기본 | 상품 카드 그리드 표시, 인기순 정렬 |
| 빈 상태 | "검색 결과가 없습니다" 메시지와 조건 재확인 안내 |

## 인터랙션

- 정렬 변경 → 상품 목록 재정렬
- 상품 카드 클릭 → 상품 상세 화면으로 이동
- 페이지 버튼 클릭 → 다음 결과 페이지 조회

requirements: ../../screens/PRODUCT_LIST/requirements.md#상품-목록-탐색
```

## 2단계: 화면 명세

`docs/screens/PRODUCT_LIST/index.md`

```markdown
---
screenId: PRODUCT_LIST
title: 상품 목록
purpose: 사용자가 카테고리와 가격으로 상품을 필터링하고 원하는 상품을 찾는 화면
viewport: pc
---

# 상품 목록

## 레이아웃

1. 상단 헤더 — 로고, 검색 입력, 장바구니, 프로필
2. 좌측 사이드바 — [@search-filter](../../features/search-filter/index.md)
3. 메인 목록 영역 — [@product-list](../../features/product-list/index.md)
```

## 3단계: 화면 요구사항

`docs/screens/PRODUCT_LIST/requirements.md`

```markdown
# 상품 목록 Requirements

## 유저스토리

### 검색 및 필터
- 사용자로서 카테고리와 가격 조건으로 상품을 좁혀 보고 싶다, 원하는 상품만 빠르게 찾기 위해

### 상품 목록 탐색
- 사용자로서 정렬 기준을 바꾸며 상품을 비교하고 싶다, 내 기준에 맞는 상품을 고르기 위해
- 사용자로서 검색 결과가 없을 때 현재 조건이 너무 좁은지 바로 파악하고 싶다, 조건을 다시 조정하기 위해

## 인수조건

### 검색 및 필터
- Given 카테고리와 가격 범위를 선택 When 필터 적용 버튼 클릭 Then 조건이 상품 목록에 반영된다
- Given 필터가 적용된 상태 When 필터 초기화 클릭 Then 기본 목록으로 돌아간다

### 상품 목록 탐색
- Given 상품 목록이 존재 When 사용자가 정렬 기준을 변경 Then 동일한 결과 집합이 새 순서로 다시 표시된다
- Given 조건에 맞는 상품이 없음 When 목록 조회 완료 Then 상품 카드 대신 빈 상태 안내가 표시된다
```

## 4단계: 생성된 와이어프레임

`docs/screens/PRODUCT_LIST/wireframe.html`

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>상품 목록</title>
  <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>

  <style type="text/tailwindcss">
    @custom-variant dark (&:where(.dark, .dark *));

    .feature-review:has(> input:nth-of-type(1):checked) .review-tabs label:nth-of-type(1),
    .feature-review:has(> input:nth-of-type(2):checked) .review-tabs label:nth-of-type(2),
    .feature-review:has(> input:nth-of-type(3):checked) .review-tabs label:nth-of-type(3),
    .feature-review:has(> input:nth-of-type(4):checked) .review-tabs label:nth-of-type(4) {
      @apply bg-white text-zinc-900 font-semibold shadow-sm dark:bg-zinc-800 dark:text-zinc-100;
    }
  </style>

  <script type="application/json" id="flowframe-meta">
  {
    "generator": "flowframe-wireframe-skill",
    "version": "1.2",
    "screenId": "PRODUCT_LIST",
    "title": "상품 목록",
    "viewport": "pc",
    "purpose": "사용자가 카테고리와 가격으로 상품을 필터링하고 원하는 상품을 찾는 화면",
    "features": [
      {
        "featureId": "SEARCH_FILTER",
        "label": "검색/필터",
        "spec": "../../features/search-filter/index.md",
        "elements": [
          { "id": "FEATURE_SEARCH_FILTER_CATEGORY", "type": "list", "label": "카테고리 필터", "description": "사용자가 관심 상품 카테고리만 남기도록 체크박스 조건을 조정하는 필터 목록" },
          { "id": "FEATURE_SEARCH_FILTER_PRICE", "type": "input", "label": "가격 범위", "description": "예산 구간만 조회되도록 최소 가격과 최대 가격을 입력하는 필터 영역" },
          { "id": "FEATURE_SEARCH_FILTER_APPLY", "type": "button", "label": "필터 적용 버튼", "description": "선택한 카테고리와 가격 조건을 실제 목록 조회 결과에 반영하는 실행 버튼" },
          { "id": "FEATURE_SEARCH_FILTER_RESET", "type": "link", "label": "필터 초기화", "description": "현재 적용된 필터 조건을 모두 제거하고 전체 상품 목록으로 되돌리는 초기화 링크" }
        ]
      },
      {
        "featureId": "PRODUCT_LIST",
        "label": "상품 목록",
        "spec": "../../features/product-list/index.md",
        "elements": [
          { "id": "FEATURE_PRODUCT_LIST_SORT", "type": "select", "label": "정렬 선택", "description": "인기순, 최신순, 가격순 중 하나를 골라 같은 결과 집합의 우선순위를 바꾸는 정렬 선택 영역" },
          { "id": "FEATURE_PRODUCT_LIST_CARDS", "type": "list", "label": "상품 카드", "description": "상품 이미지, 이름, 가격, 평점을 카드 단위로 보여 주는 메인 결과 목록" },
          { "id": "FEATURE_PRODUCT_LIST_PAGINATION", "type": "button", "label": "페이지네이션", "description": "현재 결과 페이지를 기준으로 이전이나 다음 결과 묶음으로 이동하는 하단 제어 영역" }
        ]
      }
    ]
  }
  </script>
</head>
<body class="bg-zinc-50 dark:bg-zinc-900 text-zinc-800 dark:text-zinc-200">
  <div class="flex min-h-screen min-w-[1200px] flex-col">
    <header class="h-14 border-b border-zinc-200 bg-white px-6 dark:border-zinc-700 dark:bg-zinc-800">
      <div class="flex h-full items-center justify-between gap-6">
        <div class="flex items-center gap-3">
          <div class="h-8 w-8 rounded-lg bg-zinc-200 dark:bg-zinc-700"></div>
          <span class="text-sm font-semibold">ShopName</span>
        </div>
        <div class="flex-1 max-w-xl">
          <input class="h-9 w-full rounded-md border border-zinc-300 bg-transparent px-3 text-sm dark:border-zinc-600" type="text" placeholder="상품 검색" readonly />
        </div>
        <div class="flex items-center gap-4">
          <div class="h-6 w-6 rounded bg-zinc-200 dark:bg-zinc-700"></div>
          <div class="h-8 w-8 rounded-full bg-zinc-200 dark:bg-zinc-700"></div>
        </div>
      </div>
    </header>

    <div class="flex flex-1 overflow-hidden">
      <aside data-feature-id="SEARCH_FILTER" data-feature-label="검색/필터" class="w-72 border-r border-zinc-200 bg-white p-5 dark:border-zinc-700 dark:bg-zinc-800">
        <div class="flex items-center justify-between">
          <h2 class="text-sm font-semibold">필터</h2>
          <a class="text-xs text-zinc-500 hover:underline dark:text-zinc-400" href="#" data-feature="FEATURE_SEARCH_FILTER_RESET">초기화</a>
        </div>

        <div class="mt-5 flex flex-col gap-5">
          <div data-feature="FEATURE_SEARCH_FILTER_CATEGORY">
            <div class="mb-2 text-xs font-semibold uppercase text-zinc-500 dark:text-zinc-400">카테고리</div>
            <div class="flex flex-col gap-2">
              <label class="flex items-center gap-2 text-sm">
                <input class="h-4 w-4 rounded border-zinc-300 dark:border-zinc-600" type="checkbox" checked disabled />
                전자기기
              </label>
              <label class="flex items-center gap-2 text-sm">
                <input class="h-4 w-4 rounded border-zinc-300 dark:border-zinc-600" type="checkbox" disabled />
                의류
              </label>
              <label class="flex items-center gap-2 text-sm">
                <input class="h-4 w-4 rounded border-zinc-300 dark:border-zinc-600" type="checkbox" disabled />
                생활용품
              </label>
              <label class="flex items-center gap-2 text-sm">
                <input class="h-4 w-4 rounded border-zinc-300 dark:border-zinc-600" type="checkbox" disabled />
                도서
              </label>
            </div>
          </div>

          <div data-feature="FEATURE_SEARCH_FILTER_PRICE">
            <div class="mb-2 text-xs font-semibold uppercase text-zinc-500 dark:text-zinc-400">가격 범위</div>
            <div class="flex items-center gap-2">
              <input class="h-9 w-full rounded-md border border-zinc-300 bg-transparent px-2 text-sm dark:border-zinc-600" type="text" value="30,000" readonly />
              <span class="text-zinc-400">~</span>
              <input class="h-9 w-full rounded-md border border-zinc-300 bg-transparent px-2 text-sm dark:border-zinc-600" type="text" value="200,000" readonly />
            </div>
          </div>

          <button class="h-9 rounded-md bg-zinc-800 text-sm font-semibold text-white dark:bg-zinc-200 dark:text-zinc-900" data-feature="FEATURE_SEARCH_FILTER_APPLY">
            필터 적용
          </button>
        </div>
      </aside>

      <main class="flex-1 overflow-y-auto p-6">
        <div data-feature-id="PRODUCT_LIST" data-feature-label="상품 목록" class="feature-review group/product-list relative">
          <input class="peer/default sr-only" type="radio" name="product-list-review" id="product-list-default" checked />
          <input class="peer/empty sr-only" type="radio" name="product-list-review" id="product-list-empty" />

          <div class="review-tabs absolute right-0 -top-3 z-20 flex items-center gap-1 rounded-full border border-dashed border-zinc-300 bg-zinc-50/95 px-1.5 py-1 opacity-0 shadow-sm pointer-events-none translate-y-1 transition-all group-hover/product-list:pointer-events-auto group-hover/product-list:translate-y-0 group-hover/product-list:opacity-100 group-focus-within/product-list:pointer-events-auto group-focus-within/product-list:translate-y-0 group-focus-within/product-list:opacity-100 dark:border-zinc-600 dark:bg-zinc-900/95">
            <label for="product-list-default" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">
              기본
            </label>
            <label for="product-list-empty" class="cursor-pointer rounded-full px-2.5 py-1 text-[11px] font-medium text-zinc-500 dark:text-zinc-400 transition">
              결과 없음
            </label>
          </div>

          <div class="hidden peer-checked/default:block">
            <div class="mb-5 flex items-center justify-between">
              <span class="text-sm text-zinc-500 dark:text-zinc-400">총 128개 상품</span>
              <div data-feature="FEATURE_PRODUCT_LIST_SORT">
                <select class="h-9 rounded-md border border-zinc-300 bg-transparent px-3 text-sm dark:border-zinc-600" disabled>
                  <option>인기순</option>
                  <option>최신순</option>
                  <option>가격 낮은순</option>
                  <option>가격 높은순</option>
                </select>
              </div>
            </div>

            <div class="grid grid-cols-3 gap-4" data-feature="FEATURE_PRODUCT_LIST_CARDS">
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">무선 블루투스 이어폰</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩45,000</span>
                  <span class="text-xs text-zinc-400">★★★★☆ (234)</span>
                </div>
              </div>
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">스마트 워치 프로</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩189,000</span>
                  <span class="text-xs text-zinc-400">★★★★★ (89)</span>
                </div>
              </div>
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">USB-C 충전 케이블</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩12,900</span>
                  <span class="text-xs text-zinc-400">★★★★☆ (1,024)</span>
                </div>
              </div>
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">노이즈 캔슬링 헤드폰</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩320,000</span>
                  <span class="text-xs text-zinc-400">★★★★★ (567)</span>
                </div>
              </div>
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">미니 보조 배터리</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩29,900</span>
                  <span class="text-xs text-zinc-400">★★★☆☆ (412)</span>
                </div>
              </div>
              <div class="rounded-lg border border-zinc-200 bg-white p-3 dark:border-zinc-700 dark:bg-zinc-800">
                <div class="aspect-square rounded-md bg-zinc-200 dark:bg-zinc-700"></div>
                <div class="mt-3 flex flex-col gap-1">
                  <span class="text-sm font-semibold">기계식 텐키리스 키보드</span>
                  <span class="text-sm text-zinc-500 dark:text-zinc-400">₩89,000</span>
                  <span class="text-xs text-zinc-400">★★★★☆ (178)</span>
                </div>
              </div>
            </div>

            <div class="mt-6 flex items-center justify-center gap-2" data-feature="FEATURE_PRODUCT_LIST_PAGINATION">
              <button class="h-9 rounded-md border border-zinc-300 px-3 text-sm font-semibold text-zinc-600 dark:border-zinc-600 dark:text-zinc-400">이전</button>
              <button class="h-9 w-9 rounded-md bg-zinc-800 text-sm font-semibold text-white dark:bg-zinc-200 dark:text-zinc-900">1</button>
              <button class="h-9 w-9 text-sm text-zinc-500 dark:text-zinc-400">2</button>
              <button class="h-9 w-9 text-sm text-zinc-500 dark:text-zinc-400">3</button>
              <span class="text-zinc-400">...</span>
              <button class="h-9 w-9 text-sm text-zinc-500 dark:text-zinc-400">12</button>
              <button class="h-9 rounded-md border border-zinc-300 px-3 text-sm font-semibold text-zinc-600 dark:border-zinc-600 dark:text-zinc-400">다음</button>
            </div>
          </div>

          <div class="hidden peer-checked/empty:block">
            <div class="mb-5 flex items-center justify-between">
              <span class="text-sm text-zinc-500 dark:text-zinc-400">총 0개 상품</span>
              <div data-feature="FEATURE_PRODUCT_LIST_SORT">
                <select class="h-9 rounded-md border border-zinc-300 bg-transparent px-3 text-sm dark:border-zinc-600" disabled>
                  <option>인기순</option>
                </select>
              </div>
            </div>

            <div data-feature="FEATURE_PRODUCT_LIST_CARDS" class="rounded-xl border border-dashed border-zinc-300 bg-white p-10 text-center dark:border-zinc-600 dark:bg-zinc-800">
              <div class="mx-auto h-12 w-12 rounded-full bg-zinc-100 dark:bg-zinc-700"></div>
              <div class="mt-4 text-base font-semibold text-zinc-900 dark:text-zinc-100">검색 결과가 없습니다</div>
              <p class="mt-2 text-sm text-zinc-500 dark:text-zinc-400">현재 필터 조건을 다시 확인하거나 일부 조건을 해제해 보세요.</p>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</body>
</html>
```

## 핵심 패턴

1. **두 feature, 두 영역**: `SEARCH_FILTER`는 좌측 사이드바, `PRODUCT_LIST`는 메인 목록 영역을 차지한다.
2. **명세 기반 고정 영역만 유지**: 상단 헤더는 화면 명세에 명시돼 있으므로 유지되고, 메타데이터에는 포함되지 않는다.
3. **PC 기준선 명시**: 바깥 셸이 `min-w-[1200px]`를 가져 가이드의 PC 폭 규칙을 그대로 보여 준다.
4. **리뷰 탭은 제품 UI 밖**: 목록 상태 비교는 feature 위의 hover/focus 오버레이로만 드러난다.
5. **활성 탭은 `:has()` CSS**: 패널 전환은 `peer-checked`(형제 관계), 탭 라벨 활성 스타일은 `<style>` 내 `:has()` 규칙으로 처리. 라벨에 `peer-checked` 클래스 없음. group 이름 `group/product-list`로 hover 스코프 유지.
6. **추적 ID는 상태 패널에서만 반복**: `PRODUCT_LIST`의 같은 추적 요소는 상호배타 패널 안에서만 반복된다.
7. **요구사항은 화면 수준에 배치**: 각 feature는 `requirements:` 포인터만 들고, 유저스토리와 인수조건은 화면 `requirements.md`에 있다.
8. **description은 비즈니스 의미 포함**: 메타데이터 `description`은 단순 라벨이 아니라 화면에서의 역할과 판단 맥락을 설명한다.
9. **2-tier DOM 추적**: `<aside data-feature-id="SEARCH_FILTER" data-feature-label="검색/필터">`는 코멘트 앵커, 내부 `data-feature`는 하이라이트 대상. `PRODUCT_LIST`도 동일 패턴.
