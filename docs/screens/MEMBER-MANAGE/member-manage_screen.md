---
screenId: MEMBER-MANAGE
title: 회원 관리
purpose: 서비스 운영팀 관리자가 회원 목록을 조회·검색·필터링하고, 회원 상세 정보를 확인·수정하며, 회원 상태 및 역할을 관리하는 화면
viewport: [pc, mobile]
features: [MEMBER]
---

## Screen

### 레이아웃 (PC)

1. 상단 헤더
2. 좌측 회원 목록 패널 — @MEMBER/MEMBER_LIST
3. 우측 회원 상세 패널 — @MEMBER/MEMBER_DETAIL
모달: 상태 변경 확인 [status-change] — @MEMBER/MEMBER_STATUS
모달: 역할 변경 [role-change] — @MEMBER/MEMBER_ROLE
4. 하단 푸터

### 레이아웃 (Mobile)

1. 상단 헤더
2. 회원 목록 (전체 화면) — @MEMBER/MEMBER_LIST
3. 회원 상세 (목록에서 선택 시 전체 화면 전환) — @MEMBER/MEMBER_DETAIL
모달: 상태 변경 확인 [status-change] — @MEMBER/MEMBER_STATUS
모달: 역할 변경 [role-change] — @MEMBER/MEMBER_ROLE
4. 하단 푸터

## Requirement

### 회원 목록 조회 — @MEMBER/MEMBER_LIST
- Given 회원 관리 화면 진입 When 초기 로드 완료 Then 전체 회원 목록이 테이블에 이름, 이메일, 가입일, 상태, 역할 컬럼으로 표시된다
- Given 검색 입력에 "홍길동" 입력 When 검색 실행 Then 이름에 "홍길동"이 포함된 회원만 목록에 표시된다
- Given 상태 필터에서 "정지" 선택 When 필터 적용 Then 상태가 "정지"인 회원만 목록에 표시된다
- Given 검색/필터 조건에 맞는 회원이 0명 When 조회 완료 Then 테이블 대신 "조건에 맞는 회원이 없습니다" 빈 상태 안내가 표시된다

### 회원 상세 조회 및 수정 — @MEMBER/MEMBER_DETAIL
- Given 좌측 목록에서 회원 행 클릭 When 상세 패널 로드 완료 Then 해당 회원의 프로필 정보(이름, 이메일, 가입일, 상태, 역할)와 메모가 우측 패널에 표시된다
- Given 상세 패널에서 메모 필드 클릭 When 편집 모드 활성화 Then 메모 텍스트가 편집 가능한 상태로 전환된다
- Given 메모 수정 후 포커스 아웃 When 자동 저장 실행 Then 변경된 메모가 저장되고 저장 완료 표시가 나타난다
- Given 좌측 목록에서 아무 회원도 선택하지 않은 상태 When 상세 패널 조회 Then "회원을 선택해주세요" 안내 메시지가 표시된다

### 회원 상태 변경 — @MEMBER/MEMBER_STATUS
- Given 상세 패널에서 상태 변경 버튼 클릭 When 모달 오픈 Then 현재 상태와 변경할 상태 선택, 사유 입력 필드가 표시된다
- Given 변경 사유 미입력 상태 When 확인 버튼 확인 Then 확인 버튼이 비활성화 상태이다
- Given 변경할 상태와 사유를 모두 입력 When 확인 버튼 클릭 Then 회원 상태가 변경되고 모달이 닫히며 상세 패널이 갱신된다
- Given 본인 계정의 상세 패널 When 상태 변경 버튼 확인 Then 버튼이 비활성화되어 클릭할 수 없다

### 회원 역할 변경 — @MEMBER/MEMBER_ROLE
- Given 상세 패널에서 역할 변경 버튼 클릭 When 모달 오픈 Then 현재 역할과 변경할 역할 선택이 표시된다
- Given 변경할 역할 선택 완료 When 확인 버튼 클릭 Then 회원 역할이 변경되고 모달이 닫히며 상세 패널이 갱신된다

## UserStory

### 회원 목록 조회 — @MEMBER/MEMBER_LIST
- 관리자로서 회원 목록을 이름이나 이메일로 검색하고 싶다, 특정 회원을 빠르게 찾기 위해
- 관리자로서 상태별로 회원을 필터링하고 싶다, 정지된 회원이나 탈퇴한 회원을 한눈에 파악하기 위해

### 회원 상세 조회 및 수정 — @MEMBER/MEMBER_DETAIL
- 관리자로서 회원의 프로필 정보를 한눈에 보고 싶다, 회원 문의 대응 시 빠르게 정보를 확인하기 위해
- 관리자로서 회원에 대한 메모를 바로 작성하고 싶다, 운영 이력을 기록하기 위해

### 회원 상태 변경 — @MEMBER/MEMBER_STATUS
- 관리자로서 문제 있는 회원의 상태를 정지로 변경하고 싶다, 서비스 이용을 제한하기 위해
- 관리자로서 상태 변경 사유를 기록하고 싶다, 변경 이력을 투명하게 관리하기 위해

### 회원 역할 변경 — @MEMBER/MEMBER_ROLE
- 관리자로서 회원에게 VIP 역할을 부여하고 싶다, 특별 혜택 대상을 지정하기 위해
- 관리자로서 역할 변경 시 확인 절차를 거치고 싶다, 실수로 역할을 변경하는 것을 방지하기 위해
