# Turborepo Environment Variables

## Trigger

turborepo 프로젝트에서 환경 변수 추가/수정 시 적용

## Required Action

환경 변수 변경 시 반드시 `turbo.json` 업데이트 필요

### Classification

**`tasks.build.env`** - 빌드 결과물에 영향:
- `NEXT_PUBLIC_*` 접두사 변수
- 빌드 시점에 번들에 포함되는 변수

**`tasks.build.passThroughEnv`** - 런타임에만 필요:
- DB 연결: `DATABASE_URL`
- 시크릿: `*_SECRET`, `*_API_KEY`
- 외부 서비스 인증 정보

## Checklist

환경 변수 작업 완료 전:
- [ ] `turbo.json`의 `env` 또는 `passThroughEnv`에 추가
- [ ] 적절한 카테고리에 배치

## Warning

`turbo.json` 미등록 시:
- Vercel에서 캐시된 이전 빌드 사용 가능
- 환경 변수 값이 빌드에 미반영
