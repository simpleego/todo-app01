# API_SPEC - Todo App

DATA_MODEL.md의 `todos` 테이블을 다루는 API. PRD.md의 F-01~F-04에 대응한다. (F-05 데이터 유지는 별도 엔드포인트 없이 DB 저장 자체로 충족된다.)

## 공통 규칙
- Base URL: `/api` (예: `/api/todos`)
- 요청/응답 모두 `Content-Type: application/json`
- 모든 에러 응답은 아래 형식으로 통일한다.

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "title은 1~100자여야 합니다."
  }
}
```

- 공통 에러 코드

| code | 상태 코드 | 상황 |
|---|---|---|
| VALIDATION_ERROR | 400 | 요청 값이 조건(필수, 길이 등)을 만족하지 않음 |
| NOT_FOUND | 404 | 지정한 id의 할 일이 존재하지 않음 |
| INTERNAL_ERROR | 500 | 서버/DB 처리 중 예상치 못한 오류 |

> 구현 메모: FastAPI 기본 검증 오류는 `{"detail": [...]}` 형식이라 위 형식과 다르다. 구현 시 커스텀 예외 핸들러로 통일해야 한다. (코드 작성은 이후 단계)

---

## POST /api/todos — 할 일 추가 (F-01)

**요청**
```json
{
  "title": "우유 사기"
}
```

**성공 응답 — 201 Created**
```json
{
  "id": 1,
  "title": "우유 사기",
  "isDone": false,
  "createdAt": "2026-10-18T12:00:00Z"
}
```

**에러**

| 상황 | 상태 코드 | 응답 예시 |
|---|---|---|
| title 누락 또는 빈 문자열 | 400 | `{"error":{"code":"VALIDATION_ERROR","message":"title은 필수입니다."}}` |
| title이 100자 초과 | 400 | `{"error":{"code":"VALIDATION_ERROR","message":"title은 1~100자여야 합니다."}}` |

---

## GET /api/todos — 할 일 목록 조회, 최신순 (F-02)

**요청**: 없음 (쿼리 파라미터 없음)

**성공 응답 — 200 OK**
```json
[
  {
    "id": 2,
    "title": "보고서 작성",
    "isDone": false,
    "createdAt": "2026-10-18T13:00:00Z"
  },
  {
    "id": 1,
    "title": "우유 사기",
    "isDone": false,
    "createdAt": "2026-10-18T12:00:00Z"
  }
]
```
(createdAt 기준 내림차순 정렬. 할 일이 없으면 빈 배열 `[]`)

**에러**

| 상황 | 상태 코드 | 응답 예시 |
|---|---|---|
| DB 조회 실패 등 서버 오류 | 500 | `{"error":{"code":"INTERNAL_ERROR","message":"목록을 불러오지 못했습니다."}}` |

---

## PATCH /api/todos/{id} — 완료/미완료 전환 (F-03)

**요청**
```json
{
  "isDone": true
}
```

**성공 응답 — 200 OK**
```json
{
  "id": 1,
  "title": "우유 사기",
  "isDone": true,
  "createdAt": "2026-10-18T12:00:00Z"
}
```

**에러**

| 상황 | 상태 코드 | 응답 예시 |
|---|---|---|
| 해당 id의 할 일 없음 | 404 | `{"error":{"code":"NOT_FOUND","message":"id 1인 할 일을 찾을 수 없습니다."}}` |
| isDone이 boolean이 아님 | 400 | `{"error":{"code":"VALIDATION_ERROR","message":"isDone은 true/false여야 합니다."}}` |

---

## DELETE /api/todos/{id} — 할 일 삭제 (F-04)

**요청**: 없음 (body 없음)

**성공 응답 — 204 No Content**
(본문 없음)

**에러**

| 상황 | 상태 코드 | 응답 예시 |
|---|---|---|
| 해당 id의 할 일 없음 | 404 | `{"error":{"code":"NOT_FOUND","message":"id 1인 할 일을 찾을 수 없습니다."}}` |
