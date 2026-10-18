# DATA_MODEL - Todo App

PRD.md의 F-01~F-05를 충족하는 데 필요한 최소 테이블만 정의한다. 로그인/카테고리/마감일 등은 범위 밖(PRD 참고)이므로 테이블을 추가하지 않는다.

## todos 테이블

| 필드 | 타입 (PostgreSQL) | 제약조건 | 설명 |
|---|---|---|---|
| id | INTEGER | PK, AUTO INCREMENT (SERIAL) | 할 일 고유 식별자 |
| title | VARCHAR(100) | NOT NULL, 길이 1~100자 | 할 일 제목 (F-01) |
| is_done | BOOLEAN | NOT NULL, DEFAULT false | 완료 여부 (F-03) |
| created_at | TIMESTAMP | NOT NULL, DEFAULT now() | 생성 시각. 목록 정렬 기준 (F-02, 최신순 = created_at DESC) |

그 외 테이블 없음. (수정 이력, 카테고리, 사용자 테이블은 현재 범위 밖)

## TypeScript 타입 예시 (참고용)

> 프론트엔드는 JavaScript로 구현하므로 실제 코드에 쓰이진 않지만, API로 주고받는 데이터 모양을 명확히 하기 위한 참고 문서다. DB 컬럼명은 snake_case(`is_done`, `created_at`)지만, 아래는 흔히 쓰는 API/프론트 관례인 camelCase로 적었다.

```typescript
// 할 일 하나의 모양 (조회/응답)
interface Todo {
  id: number;
  title: string;      // 1~100자
  isDone: boolean;
  createdAt: string;  // ISO 8601 문자열, 예: "2026-10-18T12:00:00Z"
}

// 할 일 추가 요청 (F-01)
interface TodoCreateRequest {
  title: string;       // 1~100자, 필수
}

// 완료 상태 전환 요청 (F-03)
interface TodoUpdateRequest {
  isDone: boolean;
}
```
