# Commands & Skills 통합 계획

## 배경: 왜 통합하는가

### Claude Code 플랫폼 변경

Claude Code가 **skills와 commands를 하나의 개념으로 통합**했다. 이전에는:

- **Command** (`commands/` 디렉토리): 사용자가 `/tdd`처럼 호출하는 슬래시 커맨드
- **Skill** (`skills/` 디렉토리): 자동 로드되는 참조 지식 모듈

이 둘은 별개의 시스템이었지만, 현재 Claude Code에서는 **skill이 곧 command**다. 스킬의 SKILL.md에 `name: tdd`를 선언하면 사용자가 `/tdd`로 호출할 수 있다. 별도의 command 파일이 필요 없다.

### 우리 플러그인의 문제

이 플랫폼 변경에도 불구하고, 우리 플러그인은 아직 **같은 주제에 대해 커맨드와 스킬이 각각 존재**하는 구조다. 예를 들어 `/tdd` 커맨드와 `tdd-workflow` 스킬이 둘 다 RED-GREEN-REFACTOR 사이클과 80% 커버리지 목표를 설명한다. 이는:

1. **구조적 불일치** - 플랫폼은 통합했는데 플러그인은 이원화 유지
2. **유지보수 비용 증가** - 같은 내용을 두 곳에서 관리
3. **불일치 위험** - 한쪽만 업데이트하면 정보가 어긋남
4. **토큰 낭비** - 중복 내용이 컨텍스트에 두 번 로드

### 목표

- 커맨드 내용을 대응하는 스킬에 흡수하고, 중복 커맨드 파일 삭제
- 스킬의 `name` 필드로 슬래시 커맨드 호출을 대체
- 대응 스킬이 없는 커맨드는 그대로 유지

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
- [x] `/tdd` + `tdd-workflow`
- [x] `/verify` + `verification-loop` (커맨드 + 스킬 모두 삭제, `/checkpoint`도 함께 삭제)
- [x] `/code-review` + `security-review` (skill name -> code-review, command 삭제, agent 슬림화)
- [x] `/test-coverage` → `tdd-workflow` 흡수 (Coverage Analysis 섹션 추가, command 삭제)
- [x] `/commit` 보안 체크 중복 제거 (인라인 체크리스트 -> skill 참조)
