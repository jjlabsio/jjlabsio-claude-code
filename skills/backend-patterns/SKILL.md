---
name: backend-patterns
description: Backend architecture rules - project-specific decisions for error handling, caching, logging, and data access.
---

# Backend Architecture Rules

Claude 기본 행동으로 커버되지 않는 프로젝트 고유 아키텍처 결정.

## 에러 핸들링

- 커스텀 `ApiError` 클래스를 정의하고 `statusCode`, `message`, `isOperational` 을 포함한다.
- 입력 검증 에러(Zod 등)는 400으로 반환하고 `details` 에 필드별 에러를 포함한다.

## 캐싱

- 캐시는 Repository를 감싸는 데코레이터 패턴으로 구현한다 (CachedXxxRepository).
- Service나 Controller에 캐시 로직을 넣지 않는다.
- 캐시 무효화 메서드를 반드시 함께 구현한다.

## 로깅

- 모든 요청에 `requestId`를 부여하고 로그에 포함한다.
- PII(개인정보)는 로그에 포함하지 않는다.

## 쿼리 규칙

- 여러 테이블 변경이 필요하면 반드시 트랜잭션으로 감싼다.
