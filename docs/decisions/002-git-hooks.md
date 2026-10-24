# 002. husky로 커밋 컨벤션 검사와 main 직접 push 방지

## 상태

- Accepted

## 맥락

커밋 컨벤션(`<type>: <subject>`)과 "main에 직접 push하지 않고 PR로 병합"을 사람과 AI 에이전트 모두가 지켜야 합니다. 문서 규칙만으로는 실수를 막을 수 없습니다.

## 결정

husky 9로 로컬 git 훅 두 개를 둡니다.

- `.husky/commit-msg`: 첫 줄이 `<type>: <subject>`인지, subject가 비어 있거나 마침표로 끝나지 않는지, AI 출처 문구가 없는지 검사합니다. `Merge`, `Revert`, `fixup!`, `squash!` 자동 메시지는 통과시킵니다.
- `.husky/pre-push`: 원격 `main`으로 가는 push를 막습니다.

## 근거

- 셸 스크립트 두 개로 충분해서 commitlint 같은 추가 패키지가 필요 없습니다.
- `prepare` 스크립트로 `pnpm install` 때 훅이 자동 설치되어 팀원이 따로 설정하지 않아도 됩니다.

## 대안

- commitlint: 규칙 표현력은 높지만 패키지와 설정 파일이 늘어납니다. type 7개와 형식 검사만 필요해 과합니다.
- GitHub branch protection만 사용: 서버에서 확실히 막지만 push 후에야 실패를 압니다. 로컬 훅과 함께 쓰는 것이 가장 확실합니다.

## 영향

- 로컬 훅은 `git commit -n`, `git push --no-verify`로 우회할 수 있습니다. 확실한 차단이 필요하면 GitHub 저장소 설정에서 main branch protection을 켭니다.
- 커밋 type 목록을 바꾸면 `.husky/commit-msg`와 `.claude/skills/commit/SKILL.md`를 함께 고칩니다.
