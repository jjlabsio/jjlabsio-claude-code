---
name: database-migrations
description: Database migration safety rules and zero-downtime procedures for production schema changes.
---

# Database Migration Rules

Claude 기본 행동으로 커버되지 않는 프로덕션 마이그레이션 안전 규칙.

## 안전 규칙

- DDL(스키마)과 DML(데이터)을 한 마이그레이션에 섞지 않는다.
- NOT NULL 컬럼은 반드시 default와 함께 추가한다 — 없으면 full table rewrite + lock.
- 대형 테이블 인덱스는 `CREATE INDEX CONCURRENTLY` 사용.
- 적용 전 프로덕션 크기 데이터에서 테스트한다.

## Zero-Downtime: Expand-Contract 패턴

Breaking change(컬럼 이름/타입 변경 등)는 3단계로 수행:

```
Phase 1 — EXPAND:  새 컬럼 추가(nullable/default), old+new 모두 쓰기, backfill
Phase 2 — MIGRATE: new에서 읽기, 데이터 정합성 검증
Phase 3 — CONTRACT: old 삭제
```

**코드 제거가 먼저, 컬럼 삭제는 다음 배포.** 순서를 뒤집지 않는다.
