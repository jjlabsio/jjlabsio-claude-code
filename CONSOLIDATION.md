# Commands & Skills 통합 계획

## 배경: 왜 통합하는가

Claude Code 플러그인 시스템에서 **commands**(슬래시 커맨드)와 **skills**(도메인 지식)는 원래 역할이 구분되어 있었다:

- **Command**: 사용자가 `/tdd`처럼 호출하는 액션 트리거 → 에이전트를 호출하거나 워크플로우를 실행
- **Skill**: 자동 로드되는 참조 지식 → 패턴, 체크리스트, 베스트 프랙티스

하지만 실제로는 **같은 주제에 대해 커맨드와 스킬이 내용을 중복**하고 있다. 예를 들어 `/tdd` 커맨드와 `tdd-workflow` 스킬이 둘 다 RED-GREEN-REFACTOR 사이클과 80% 커버리지 목표를 설명한다. 이는:

1. **유지보수 비용 증가** - 같은 내용을 두 곳에서 관리
2. **불일치 위험** - 한쪽만 업데이트하면 정보가 어긋남
3. **토큰 낭비** - 중복 내용이 컨텍스트에 두 번 로드

**목표**: 각 주제별로 하나의 소스(스킬)에 통합하고, 커맨드가 필요한 경우 스킬을 참조하는 얇은 래퍼로 전환한다.

---

## 현재 상태

### 커맨드 (14개)

| 커맨드 | 호출 에이전트 | 대응 스킬 |
|--------|-------------|----------|
| `/plan` | planner | - |
| `/tdd` | tdd-guide | `tdd-workflow` |
| `/code-review` | (인라인) | `security-review` |
| `/commit` | security-reviewer, code-reviewer | `security-review` |
| `/verify` | (인라인) | `verification-loop` |
| `/test-coverage` | (인라인) | `tdd-workflow` (부분) |
| `/build-fix` | build-error-resolver | - |
| `/e2e` | e2e-runner | - |
| `/refactor-clean` | refactor-cleaner | - |
| `/update-docs` | doc-updater | - |
| `/update-codemaps` | (인라인) | - |
| `/orchestrate` | (체인) | - |
| `/checkpoint` | (인라인) | - |
| `/setup-pm` | (유틸리티) | - |

### 스킬 (11개)

| 스킬 | 대응 커맨드 | 통합 대상 |
|------|-----------|----------|
| `tdd-workflow` | `/tdd`, `/test-coverage` | **YES** |
| `verification-loop` | `/verify` | **YES** |
| `security-review` | `/code-review`, `/commit` | **YES** |
| `continuous-learning` | ~~`/learn`~~ | **완료** |
| `coding-standards` | - | 통합 불필요 (독립 참조) |
| `backend-patterns` | - | 통합 불필요 (독립 참조) |
| `frontend-patterns` | - | 통합 불필요 (독립 참조) |
| `vercel-react-best-practices` | - | 통합 불필요 (독립 참조) |
| `clickhouse-io` | - | 통합 불필요 (독립 참조) |
| `strategic-compact` | - | 통합 불필요 (독립 유틸리티) |
| `project-guidelines-example` | - | 통합 불필요 (템플릿) |

---

## 통합 항목

### 1. `/learn` + `continuous-learning` → **완료**

- **변경**: `/learn` 커맨드 삭제, 스킬 SKILL.md에 수동/자동 트리거 통합
- **결과**: `skills/continuous-learning/SKILL.md` (name: learn)

---

### 2. `/tdd` + `tdd-workflow` (우선순위: 높음)

**중복 내용**: RED-GREEN-REFACTOR 사이클, 80% 커버리지 목표, 테스트 우선 원칙

| 구분 | 커맨드에만 있는 것 | 스킬에만 있는 것 |
|------|------------------|----------------|
| 내용 | 에이전트 호출 로직, 다른 커맨드 연동, 풀 워크스루 예시 | 테스트 타입별 패턴, 모킹 예시(Supabase/Redis/OpenAI), 피해야 할 실수, 파일 구조, CI/CD |

**통합 방향**: 스킬에 커맨드의 워크플로우/호출 내용을 흡수하고, 커맨드 파일 삭제

---

### 3. `/verify` + `verification-loop` (우선순위: 높음)

**중복 내용**: 6단계 검증 프로세스 (build → types → lint → test → security → git status), 80% 커버리지

| 구분 | 커맨드에만 있는 것 | 스킬에만 있는 것 |
|------|------------------|----------------|
| 내용 | 인자 지원 (quick/full/pre-commit/pre-pr), 간결한 pass/fail 출력 | Bash 명령 예시, Python 지원, Continuous Mode (15분 체크포인트), Hook 연동 |

**통합 방향**: 스킬에 커맨드의 인자/출력 포맷을 흡수하고, 커맨드 파일 삭제

---

### 4. `/code-review` + `security-review` (우선순위: 중간)

**중복 내용**: 보안 체크리스트 (시크릿, SQL 인젝션, XSS, 입력 검증)

| 구분 | 커맨드에만 있는 것 | 스킬에만 있는 것 |
|------|------------------|----------------|
| 내용 | git diff 워크플로우, 심각도 분류, 커밋 차단 로직 | 10개 보안 도메인 상세 패턴, 코드 예시, 블록체인 보안, 배포 전 체크리스트, 테스트 패턴 |

**통합 방향**: 스킬에 커맨드의 리뷰 워크플로우를 흡수하고, 커맨드 파일 삭제

---

### 5. `/test-coverage` + `tdd-workflow` (우선순위: 낮음)

**관계**: 상호 보완적 (커맨드=테스트 생성 실행, 스킬=커버리지 설정/목표)

**통합 방향**: 커맨드 내용을 `tdd-workflow` 스킬에 흡수. 중복은 적지만 독립 커맨드로 존재할 필요 없음.

---

### 6. `/commit` + `security-review` (우선순위: 낮음)

**관계**: `/commit`은 보안 체크 외에 git 워크플로우, 커밋 메시지 포맷 등 독자적 로직이 많음

**통합 방향**: `/commit`은 독립 커맨드로 유지. 다만 보안 체크리스트 중복 부분은 스킬 참조로 교체.

---

## 통합 원칙

1. **스킬이 정보의 단일 소스** - 패턴, 체크리스트, 참조 지식은 스킬에만 존재
2. **커맨드는 얇은 래퍼** - 에이전트 호출이나 고유 워크플로우가 있는 경우만 커맨드로 유지
3. **단순 중복이면 커맨드 삭제** - 스킬과 내용이 겹치고 고유 로직이 없으면 스킬에 흡수
4. **점진적 진행** - 한 번에 하나씩 통합하고 테스트

## 진행 상황

- [x] `/learn` + `continuous-learning`
- [ ] `/tdd` + `tdd-workflow`
- [ ] `/verify` + `verification-loop`
- [ ] `/code-review` + `security-review`
- [ ] `/test-coverage` → `tdd-workflow` 흡수
- [ ] `/commit` 보안 체크 중복 제거
