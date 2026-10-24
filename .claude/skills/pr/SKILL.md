---
name: pr
description: 현재 브랜치의 커밋과 변경을 정리해 PR 템플릿에 맞춘 제목과 본문 초안을 작성합니다. PR 생성은 사용자가 요청할 때만 합니다.
argument-hint: "[base 브랜치, 기본 main]"
---

# PR 초안 작성

## 현재 브랜치

- 브랜치: !`git branch --show-current`
- 커밋: !`git log main..HEAD --oneline`
- 변경 규모: !`git diff main...HEAD --stat`
- 커밋 안 된 변경: !`git status --short`

인자로 base 브랜치를 받으면 `main` 대신 그 브랜치 기준으로 다시 확인합니다.

## 순서

1. 커밋 안 된 변경이 있으면 PR에 포함되지 않는다고 먼저 알립니다.
2. 이슈 번호는 브랜치 이름에서 찾습니다. 이슈로 자동 생성된 브랜치는 `<type>/<이슈번호>-<작업>` 형식입니다(`.github/workflows/create-branch-on-issue.yml`). 번호가 없으면 `없음`으로 씁니다.
3. `git diff main...HEAD`로 실제 변경을 읽고, `.github/PULL_REQUEST_TEMPLATE.md`의 섹션 이름과 순서를 그대로 채웁니다.
4. 제목은 커밋 컨벤션과 같은 `<type>: <subject>` 형식으로 제안합니다(type 목록은 `.claude/skills/commit/SKILL.md`).
5. 사용자가 생성을 요청하면 본문을 임시 파일로 쓰고 `gh pr create --title "<제목>" --body-file <파일>`로 만듭니다.

## 지키는 것

- `셀프 체크리스트`는 이번 작업에서 실제로 실행하거나 확인한 항목만 체크합니다. WIP가 아니면 `작업 진행 중`은 체크하지 않습니다.
- 시뮬레이터/기기에서 보지 못한 화면 동작, 네이티브 모듈·`app.json`·config plugin 변경으로 development build가 필요한 점은 `리뷰 요구사항`에 적습니다.
- `변경 사항`은 파일 나열이 아니라 무엇이 왜 바뀌었는지로 씁니다.
- AI 출처 문구를 넣지 않습니다.
