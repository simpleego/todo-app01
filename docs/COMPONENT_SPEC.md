# COMPONENT_SPEC - Todo App

PRD.md(F-01~F-04)와 API_SPEC.md의 엔드포인트를 화면 컴포넌트로 어떻게 나눌지 정의한다. 프론트엔드는 JavaScript(React)이므로 Props는 TS 타입이 아닌 표로 정리한다.

## 컴포넌트 트리

```
App
 └─ TodoPage              (API 호출, 상태 보유)
     ├─ TodoForm           (할 일 추가 입력창 + 버튼)
     └─ TodoList           (목록 영역 — 로딩/에러/빈 목록/정상 목록 분기)
         └─ TodoItem       (할 일 1개: 체크박스, 제목, 삭제 버튼)  (반복)
```

## 컴포넌트별 Props

### TodoPage
Props 없음 (최상위 컴포넌트). `todos`, `loading`, `error` 상태를 직접 보유하고 API 호출(`GET/POST/PATCH/DELETE /api/todos`)을 수행한다.

### TodoForm

| Prop | 타입 | 설명 |
|---|---|---|
| onAdd | function(title: string) | 추가 버튼 클릭 시 호출되는 콜백. 호출되면 상위에서 `POST /api/todos` 수행 |
| disabled | boolean | 요청 중일 때 버튼을 비활성화할지 여부 |

### TodoList

| Prop | 타입 | 설명 |
|---|---|---|
| todos | Todo[] (id, title, isDone, createdAt 배열) | 화면에 표시할 할 일 목록 (최신순 정렬된 상태로 전달받음) |
| loading | boolean | 목록 조회 중 여부 |
| error | string \| null | 에러 발생 시 표시할 메시지, 없으면 null |
| onToggle | function(id: number, isDone: boolean) | 체크박스 클릭 시 호출. 상위에서 `PATCH /api/todos/{id}` 수행 |
| onDelete | function(id: number) | 삭제 버튼 클릭 시 호출. 상위에서 `DELETE /api/todos/{id}` 수행 |

### TodoItem

| Prop | 타입 | 설명 |
|---|---|---|
| todo | Todo (id, title, isDone, createdAt) | 할 일 1개 데이터 |
| onToggle | function(id: number, isDone: boolean) | 체크박스 클릭 시 TodoList로부터 받은 콜백 그대로 호출 |
| onDelete | function(id: number) | 삭제 버튼 클릭 시 TodoList로부터 받은 콜백 그대로 호출 |

## 사용자 동작과 결과

| 동작 | 처리 흐름 | 관련 API |
|---|---|---|
| TodoForm에 제목 입력 후 추가 버튼 클릭 | 클라이언트에서 먼저 빈 값/100자 초과 검사 → 통과 시 onAdd 호출 → 성공하면 입력창 비우고 목록 맨 위에 새 항목 표시, 실패하면 폼 하단에 에러 문구 표시 | POST /api/todos (F-01) |
| 할 일의 체크박스 클릭 | onToggle 호출 → 성공하면 해당 TodoItem의 완료 표시(취소선 등) 즉시 갱신, 실패하면 체크 상태를 원래대로 되돌리고 에러 안내 | PATCH /api/todos/{id} (F-03) |
| 할 일의 삭제 버튼 클릭 | onDelete 호출 → 성공하면 해당 TodoItem을 목록에서 제거, 실패하면 에러 안내만 표시(목록 유지) | DELETE /api/todos/{id} (F-04) |
| 페이지 진입/새로고침 | TodoPage가 마운트되며 목록을 조회해 화면에 표시 | GET /api/todos (F-02, F-05) |

## 상태별 문구

| 상태 | 조건 | 문구 |
|---|---|---|
| 로딩 중 | GET /api/todos 응답 대기 중 | "불러오는 중..." |
| 빈 목록 | 로딩 완료 + todos가 빈 배열 | "아직 등록된 할 일이 없어요. 첫 번째 할 일을 추가해보세요!" |
| 목록 조회 에러 | GET /api/todos가 에러 응답(API_SPEC의 INTERNAL_ERROR 등) | "할 일을 불러오지 못했습니다. 잠시 후 다시 시도해주세요." |
| 추가 실패 (검증) | POST /api/todos가 VALIDATION_ERROR(400) 응답 | API 응답의 `error.message`를 그대로 폼 하단에 표시 (예: "title은 1~100자여야 합니다.") |
| 토글/삭제 실패 (대상 없음) | PATCH 또는 DELETE가 NOT_FOUND(404) 응답 | "이미 삭제되었거나 존재하지 않는 항목입니다." 후 목록 다시 불러오기 |
