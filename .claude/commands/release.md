---
description: Commit changes, bump plugin version, and push
---

# Release

이 플러그인 작업 후 버전업 + 커밋 + 푸쉬를 자동화하는 커맨드.

## Workflow

1. **변경사항 확인**: `git status`로 uncommitted 변경사항 확인

2. **변경사항이 있는 경우:**
   - `/commit` 커맨드 워크플로우로 변경사항 커밋
   - 이후 step 3으로 진행

3. **docs/plans 정리:**
   - `docs/plans` 디렉토리가 존재하는지 확인
   - 존재하면 삭제 후 별도 커밋:
     - `rm -rf docs/plans`
     - `docs/` 디렉토리가 비어있으면 `docs/`도 함께 삭제
     - `git add -A docs/plans` (또는 `docs/` 전체가 삭제된 경우 `git add -A docs/`)
     - `git commit -m "chore: clean up docs/plans"`
   - 존재하지 않으면 그대로 진행

4. **변경사항이 없는 경우 (step 1에서 변경사항이 없었을 때):**
   - 마지막 버전업 커밋 찾기: `git log --oneline --grep="^chore: bump version" -1`
   - 그 이후에 새 커밋이 있는지 확인: `git log --oneline <last_version_commit>..HEAD`
   - 새 커밋이 없으면 "릴리즈할 변경사항이 없습니다" 알리고 종료
   - 새 커밋이 있으면 step 5로 진행

5. **버전 bump:**
   - `.claude-plugin/plugin.json`에서 현재 버전 읽기
   - patch 버전 올리기 (예: 1.0.4 -> 1.0.5)
   - 두 파일 모두 업데이트 (반드시 동기화):
     - `.claude-plugin/plugin.json` -> `"version"` 필드
     - `.claude-plugin/marketplace.json` -> `plugins[0].version` 필드

6. **버전 커밋:**
   - `git add .claude-plugin/plugin.json .claude-plugin/marketplace.json`
   - `git commit -m "chore: bump version to <new_version>"`

7. **Push:** `git push`
