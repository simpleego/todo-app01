# CLAUDE.md - Todo App

## 프로젝트 개요
혼자 쓰는 간단한 할 일 목록 웹 앱 (초보자 학습용 / AI 풀스택 과정 2차 프로젝트 대비용)

## 버전 로드맵
- v0.1 화면만 (브라우저 메모리)
- v0.2 API 연결 (서버 메모리)
- v0.3 DB 저장
- v0.4 필터와 수정
- v1.0 배포

## 지금은 빼는 것
- 로그인, 공유, 알림, 마감일, 카테고리 (docs/IDEA.md 참고)

## 기술 스택
AI 풀스택 과정 커리큘럼과 맞춰서 선택 (2차 프로젝트로 그대로 전이되는 것이 목표)
- 백엔드: Python 3 + FastAPI
- 프론트엔드: React (Vite, JavaScript)
- 스타일링: Tailwind CSS
- DB: PostgreSQL + SQLAlchemy ORM + Alembic 마이그레이션
- 검증: Pydantic (FastAPI 기본 제공)
- API 문서화: FastAPI 자동 생성 Swagger
- 테스트: pytest (백엔드), 로드맵 v0.3 이후 단계부터 본격 도입

## 폴더 구조
- backend/app/      : FastAPI 앱 (routers, models, schemas, database)
- frontend/src/      : React 컴포넌트, API 연동 코드
- docs/              : 명세 문서

## 반드시 참고할 문서
- docs/IDEA.md
- docs/PRD.md, docs/TRD.md, docs/DATA_MODEL.md
- docs/API_SPEC.md, docs/COMPONENT_SPEC.md, docs/TEST_CASES.md

## 규칙
- 명세에 없는 기능을 임의로 추가하지 말 것
- FastAPI 라우트와 Pydantic 스키마에는 타입을 명확히 지정할 것
- 계층을 분리할 것 (router → service/crud → model), 한 파일에 다 넣지 말 것
- 한 번에 한 기능만 작업하고, 끝나면 무엇을 바꿨는지 요약할 것
- 테스트를 삭제하거나 skip하지 말 것
- 테스트가 실패하면 테스트가 아닌 구현을 고칠 것
- 디버깅용 print()/console.log는 제거할 것
- 초보자가 이해할 수 있게 주석은 한국어로 짧게

## 자주 쓰는 명령
- 백엔드 서버: uvicorn app.main:app --reload (backend/ 에서 실행)
- 프론트엔드 서버: npm run dev (frontend/ 에서 실행)
- 백엔드 테스트: pytest (backend/ 에서 실행)
