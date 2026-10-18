# TEST_CASES - Todo App

PRD.md(F-01~F-05), API_SPEC.md, COMPONENT_SPEC.md를 바탕으로 한 테스트 케이스 목록. 테스트 코드는 아직 작성하지 않는다.

## 1. API 테스트

### POST /api/todos — 할 일 추가 (F-01)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-API-01 | POST /api/todos | title "우유 사기"로 요청 | 201, 응답에 id/title/isDone=false/createdAt 포함, DB에 실제로 저장됨 |
| TC-API-02 | POST /api/todos | title을 빈 문자열("")로 요청 | 400, error.code = VALIDATION_ERROR |
| TC-API-03 | POST /api/todos | title 필드 자체를 누락하고 요청 | 400, error.code = VALIDATION_ERROR |
| TC-API-04 | POST /api/todos | title을 101자로 요청 | 400, error.code = VALIDATION_ERROR |
| TC-API-05 | POST /api/todos | title을 정확히 1자로 요청 (경계값) | 201 성공 |
| TC-API-06 | POST /api/todos | title을 정확히 100자로 요청 (경계값) | 201 성공 |

### GET /api/todos — 목록 조회 (F-02, F-05)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-API-07 | GET /api/todos | 서로 다른 시각에 등록된 항목 3건이 있는 상태에서 조회 | 200, createdAt 내림차순(최신순)으로 정렬되어 반환 |
| TC-API-08 | GET /api/todos | 등록된 항목이 하나도 없는 상태에서 조회 | 200, 빈 배열 `[]` 반환 |
| TC-API-09 | GET /api/todos | DB 연결 실패 등 서버 오류 상황 | 500, error.code = INTERNAL_ERROR |
| TC-API-17 | POST → GET | POST로 항목 추가 후, 별도의 새 요청(새로고침 시뮬레이션)으로 GET 호출 | 방금 추가한 항목이 그대로 조회됨 (데이터 유지 확인) |

### PATCH /api/todos/{id} — 완료/미완료 전환 (F-03)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-API-10 | PATCH /api/todos/{id} | 존재하는 id에 isDone: true 요청 | 200, 응답 isDone=true |
| TC-API-11 | PATCH /api/todos/{id} | isDone=true인 항목에 isDone: false 요청 (되돌리기) | 200, 응답 isDone=false |
| TC-API-12 | PATCH /api/todos/{id} | 존재하지 않는 id로 요청 | 404, error.code = NOT_FOUND |
| TC-API-13 | PATCH /api/todos/{id} | isDone에 boolean이 아닌 값("true" 문자열 등) 전달 | 400, error.code = VALIDATION_ERROR |

### DELETE /api/todos/{id} — 삭제 (F-04)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-API-14 | DELETE /api/todos/{id} | 존재하는 id 삭제 요청 | 204, 이후 GET 목록 조회 시 해당 항목이 없음 |
| TC-API-15 | DELETE /api/todos/{id} | 존재하지 않는 id 삭제 요청 | 404, error.code = NOT_FOUND |
| TC-API-16 | DELETE /api/todos/{id} | 이미 삭제된 id를 다시 삭제 요청 | 404, error.code = NOT_FOUND |

## 2. 컴포넌트 테스트

### TodoForm

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-UI-01 | TodoForm | 제목을 입력하고 추가 버튼 클릭 | onAdd가 입력한 title 값으로 호출됨 |
| TC-UI-02 | TodoForm | 입력창을 비운 채 추가 버튼 클릭 | onAdd가 호출되지 않고, 클라이언트 검증 메시지가 표시됨 |
| TC-UI-03 | TodoForm | 101자 이상 입력 후 추가 버튼 클릭 | onAdd가 호출되지 않고, 검증 메시지가 표시됨 |
| TC-UI-04 | TodoForm | disabled=true 상태에서 추가 버튼 클릭 | 버튼이 비활성화되어 클릭이 동작하지 않음 |
| TC-UI-05 | TodoForm | 추가 요청이 성공한 직후 | 입력창이 빈 값으로 초기화됨 |

### TodoList (로딩/빈 목록/에러 상태)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-UI-06 | TodoList | loading=true로 렌더링 | "불러오는 중..." 문구가 표시됨 |
| TC-UI-07 | TodoList | loading=false, todos=[] (빈 배열)로 렌더링 | "아직 등록된 할 일이 없어요. 첫 번째 할 일을 추가해보세요!" 문구가 표시됨 |
| TC-UI-08 | TodoList | error="할 일을 불러오지 못했습니다..." 로 렌더링 | 해당 에러 문구가 화면에 표시됨 |
| TC-UI-09 | TodoList | todos에 항목 3개를 전달해 렌더링 | 전달된 순서 그대로 3개의 TodoItem이 렌더링됨 |

### TodoItem

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-UI-10 | TodoItem | 체크박스 클릭 | onToggle(id, !isDone)이 호출됨 |
| TC-UI-11 | TodoItem | isDone=true인 항목을 렌더링 | 제목에 완료 스타일(취소선 등)이 적용됨 |
| TC-UI-12 | TodoItem | 삭제 버튼 클릭 | onDelete(id)가 호출됨 |
| TC-UI-13 | TodoItem | 체크박스 클릭 후 PATCH 요청이 실패(에러 응답) | 체크 상태가 클릭 이전 상태로 롤백되고 에러 안내가 표시됨 |
| TC-UI-14 | TodoItem | 삭제 버튼 클릭 후 DELETE 요청이 404로 실패 | "이미 삭제되었거나 존재하지 않는 항목입니다." 문구 표시 후 목록이 다시 조회됨 |

### TodoPage (통합)

| ID | 대상 | 상황 | 기대 결과 |
|---|---|---|---|
| TC-UI-15 | TodoPage | 페이지 최초 진입(마운트) | GET /api/todos가 호출되고, 응답 결과가 목록에 표시됨 |
| TC-UI-16 | TodoPage | TodoForm에서 할 일 추가 성공 | POST 응답으로 받은 새 항목이 목록 맨 위(최신순)에 반영됨 |
