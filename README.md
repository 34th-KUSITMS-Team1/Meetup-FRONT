# Meetup-FRONT

야식의 민족 MVP 모바일 앱입니다. 배달앱처럼 주문하지만 결제 금액이 목표 저축 계좌로 가는 서비스입니다.
프론트엔드는 **송금 앱 딥링크 이동/복귀**와 **배달 추적 Dynamic Island(Live Activity)** 를 핵심으로 구현합니다.

| 항목 | 사용 |
| --- | --- |
| Framework | Expo SDK 58, React Native 0.88, React 19 |
| Routing | Expo Router (`src/app`, typed routes) |
| Styling | NativeWind v4 + Tailwind CSS v3 |
| Language | TypeScript (strict) |
| Package Manager | pnpm |

## 시작하기

| 명령 | 용도 |
| --- | --- |
| `pnpm install` | 의존성 설치 |
| `pnpm start` | 개발 서버 |
| `pnpm ios`, `pnpm android` | 플랫폼 지정 실행 |
| `pnpm lint` | lint |
| `pnpm exec tsc --noEmit` | 타입 체크 |
| `pnpm exec expo install <pkg>` | 의존성 추가 (`pnpm add` 대신) |

- `npm`, `yarn`, `bun`, `npx`는 사용하지 않습니다. `pnpm exec` / `pnpm dlx`를 사용합니다.
- Live Activity(`expo-widgets`)와 외부 앱 딥링크 확인은 **Expo Go에서 동작하지 않습니다.** development build(`pnpm exec expo run:ios`)로 확인합니다.

---

# AI 에이전트 규칙 안내

Claude Code와 Codex가 이 프로젝트에서 같은 기준으로 일하도록 규칙과 스킬을 저장소에 함께 둡니다.
사람도 같은 규칙을 따르면 리뷰 기준이 하나로 맞춰집니다.

## 한눈에 보기

| 위치 | 역할 |
| --- | --- |
| `AGENTS.md` | 진입점. 기술 스택, 명령어, 규칙 목록, 금지 패턴 |
| `CLAUDE.md` | `@AGENTS.md` 한 줄. Claude Code가 `AGENTS.md`를 읽게 함 |
| `.claude/rules/core/` | 항상 적용되는 규칙 |
| `.claude/rules/guides/` | 작업 종류별 규칙. 관련 파일을 열 때만 로드 |
| `.claude/rules/records/` | 결정·외부 이슈 기록 방법 |
| `.claude/skills/` | 슬래시 명령 스킬 원본 (`/commit`, `/pr`, `/check`, `/tidy`, `/test`) |
| `.claude/settings.json` | 팀 공용 설정. `settings.local.json`은 개인용이라 git 제외 |
| `.agents/skills/` | Codex용 스킬. 각 파일이 `.claude/skills` 원본을 읽도록 연결 |
| `docs/decisions/` | 결정 기록 (예: `001-folder-structure.md`) |
| `docs/known-issues/` | 외부 요인 이슈 기록. 필요할 때 생성 |
| `.github/` | PR/이슈 템플릿, 이슈 → 브랜치 자동 생성 workflow |
| `.serena/` | Serena(MCP 코드 탐색 도구) 프로젝트 설정. 캐시와 개인 설정은 폴더 내부 `.gitignore`로 제외 |

`.omc/`(oh-my-claudecode 상태)와 `.claude/settings.local.json`은 개인 환경 파일이라 `.gitignore`로 제외합니다.

## 규칙이 로드되는 방식

| 도구 | 동작 |
| --- | --- |
| Claude Code | `CLAUDE.md` → `AGENTS.md`를 읽습니다. `.claude/rules` 중 `paths` 설정이 **없는** 파일(`core/*`)은 세션 시작 시 항상 로드되고, `paths`가 **있는** 파일은 해당 경로의 파일을 읽거나 수정할 때 자동 로드됩니다. |
| Codex | `AGENTS.md`를 읽고, 표에 적힌 경로를 따라 필요한 규칙 파일을 직접 엽니다. 스킬은 `.agents/skills`에서 찾습니다. |

규칙 파일 맨 위 frontmatter의 `paths` 목록이 "언제 로드되는지"를 정합니다. 예를 들어 `guides/native.md`는 `paths`에 `src/live-activities/**/*`와 `app.json`이 있어서, 이 파일들을 다룰 때만 로드됩니다.

## 규칙 파일

### core — 항상 적용

| 파일 | 내용 |
| --- | --- |
| `core/principles.md` | 작업 원칙. 추측하지 않고 가정 밝히기, 요청하지 않은 기능·추상화 넣지 않기, 필요한 줄만 수정 |
| `core/constraints.md` | 금지/제약. pnpm만 사용, 의존성 추가 전 확인, `ios/`·`android/` 직접 수정 금지, Tailwind v4 금지, API 스펙 문서로만 확인, 서버 데이터는 TanStack Query |
| `core/workflow.md` | 작업 순서(확인 → 성공 기준 → 수정 → 검증 → 보고)와 결정·외부 이슈를 기록해야 하는 순간 |
| `core/verify.md` | 검증 기준. 코드 변경 시 `pnpm lint` + `tsc` 필수, 설정 변경 시 `expo-doctor`, 기기에서 못 본 것은 "확인 못 함"으로 보고 |

### guides — 작업할 때만 적용

| 파일 | 자동 로드 경로 | 내용 |
| --- | --- | --- |
| `guides/structure.md` | `src/**`, `tsconfig.json` | 폴더 구조, 라우트 규칙, import, 레이어 경계 |
| `guides/style.md` | `src/**/*.{ts,tsx}`, `tailwind.config.js` | TypeScript, RN 컴포넌트 사용, 클라이언트 상태, 네이밍, NativeWind 스타일 |
| `guides/ui.md` | `src/app/**/*.tsx`, `src/components/**/*.tsx` | 컴포넌트 우선순위, safe area/키보드/목록, 접근성, 컴포넌트 작성 패턴, 폼(RHF + Zod) |
| `guides/api.md` | `src/api/**`, `src/hooks/**` | API 스펙 문서 위치(추후 입력), 작업 순서, TanStack Query, API 계층, 토큰 저장 |
| `guides/testing.md` | `**/*.test.{ts,tsx}`, `__tests__/**` | jest-expo + RNTL 설정, 테스트 위치, 테스트 대상 |
| `guides/native.md` | `src/live-activities/**`, `src/**/transfer/**`, `src/**/delivery/**`, `src/app/order/**`, `+native-intent.tsx`, `app.json`, `package.json` | 송금 앱 딥링크, 앱 복귀 처리, 들어오는 딥링크, Live Activity 제약 |

### records — 기록 방법

커밋 컨벤션은 규칙 파일이 아니라 `/commit` 스킬 안에 있습니다. 커밋할 때만 필요해서 스킬로 두면 평소에는 로드되지 않습니다.

| 파일 | 자동 로드 경로 | 내용 |
| --- | --- | --- |
| `records/adr.md` | `docs/decisions/**` | 구조·라이브러리·패턴 결정을 `docs/decisions/NNN-제목.md`로 남기는 기준과 템플릿 |
| `records/issues.md` | `docs/known-issues/**` | 외부 요인(라이브러리 버그, API 스펙 불일치 등) 이슈 기록 기준과 템플릿 |

## 폴더 구조 요약

Expo 기본 템플릿의 타입별 구조를 쓰고, 그 아래를 도메인으로 나눕니다. 자세한 근거는 `docs/decisions/001-folder-structure.md`에 있습니다.

| 도메인 | 범위 |
| --- | --- |
| `goal` | 목표 설정 (목표, 금액, 계좌, 주소) |
| `order` | 매장·메뉴·장바구니·주문 |
| `transfer` | 송금 앱 이동과 복귀 확인 |
| `delivery` | 배달 추적, Live Activity 시작/갱신/종료 |
| `receipt` | 절제 영수증 |

| 폴더 | 용도 |
| --- | --- |
| `src/app/` | 라우트만. 파일 경로 = 딥링크 경로 |
| `src/components/{common,도메인}/` | 재사용 UI |
| `src/api/` | 도메인별 요청 함수 |
| `src/hooks/{common,도메인}/` | 커스텀 훅 (API 훅 포함) |
| `src/live-activities/` | `'widget'` directive 파일만. `@expo/ui/swift-ui` 외에는 import하지 않음 |
| `src/stores/`, `src/lib/`, `src/constants/` | 클라이언트 상태, 유틸, 상수 |

- `src/app`에 컴포넌트·훅·테스트를 두지 않습니다. Expo Router가 라우트로 취급합니다.
- 라우트 경로가 곧 딥링크입니다. `order/[id]/tracking.tsx`는 `meetupfront://order/123/tracking`이 되므로 경로를 바꾸면 링크를 만드는 쪽도 같이 고칩니다.
- `src/features`, `src/pages`, `src/screens` 같은 구조는 만들지 않습니다.

## 스킬 (슬래시 명령)

| 명령 | 하는 일 | 파일을 바꾸나 |
| --- | --- | --- |
| `/commit [범위]` | 변경을 읽고 커밋 메시지와 커밋 단위를 제안 | 아니요 (커밋 실행을 요청할 때만) |
| `/pr [base]` | 브랜치 이름에서 이슈 번호를 찾고 PR 템플릿에 맞춘 제목·본문 작성 | 아니요 (PR 생성을 요청할 때만) |
| `/check [경로\|ref]` | 변경 코드의 프로젝트 규칙 위반 점검 (폴더 구조, Live Activity 제약, 딥링크 설정, 데이터 패칭 등) | 아니요 |
| `/tidy [경로]` | 동작 변경 없이 파일 위치·책임 분리 정리. 후보를 먼저 보여주고 고른 것만 적용 | 예 |
| `/test [경로]` | jest-expo + RNTL 테스트 작성·실행. jest가 없으면 설치부터 제안 | 예 |

Claude Code 기본 명령과 함께 씁니다.

- 일반 버그 리뷰: `/code-review` (`/check`는 프로젝트 규칙 위반만 봄)
- 코드 단순화: `/simplify` (`/tidy`는 파일 위치·책임 분리만 봄)

> 스킬 이름은 기본 명령·별칭(`review`, `code-review`, `simplify` 등)과 겹치면 안 됩니다. 겹치면 기본 명령을 덮어쓰거나, 별칭이 우선해서 스킬이 실행되지 않습니다.

## GitHub 흐름

1. **이슈 생성**: `이슈 생성` 템플릿에서 브랜치 유형과 브랜치에 포함될 작업 내용(영문)을 입력합니다.
2. **브랜치 자동 생성**: workflow가 `<유형>/<이슈번호>-<작업 내용>` 브랜치를 만들고 이슈에 댓글을 남깁니다. 예: `feat/12-login-ui`
3. **커밋**: `<type>: <subject>` 형식. `/commit`으로 제안받을 수 있습니다.
4. **PR**: PR 템플릿의 `셀프 체크리스트`에는 실제로 확인한 항목만 체크하고, 기기에서 보지 못한 동작이나 development build 필요 여부는 `리뷰 요구사항`에 적습니다. `/pr`로 초안을 만들 수 있습니다.

## 규칙을 바꾸고 싶을 때

| 하고 싶은 것 | 방법 |
| --- | --- |
| 규칙 추가 | 주제에 맞는 `guides/` 파일에 추가합니다. 새 주제면 `guides/`에 파일을 만들고 `paths`를 지정한 뒤 `AGENTS.md` 표에 한 줄 추가합니다 |
| 항상 지켜야 하는 규칙 | `core/`에 둡니다. 매 세션 로드되므로 짧게 유지합니다 |
| 구조·라이브러리 결정 | `docs/decisions/`에 결정 기록을 남기고, 관련 규칙 파일을 함께 고칩니다 |
| 스킬 추가 | `.claude/skills/<이름>/SKILL.md`를 만들고, Codex용으로 `.agents/skills/<이름>/SKILL.md` 연결 파일을 추가한 뒤 `AGENTS.md` 스킬 표에 등록합니다 |
| 파일 이름/위치 변경 | 경로를 참조하는 `AGENTS.md`, 스킬, 다른 규칙 파일, 이 README를 함께 고칩니다 |

- 규칙 파일은 에이전트가 매번 읽는 문서입니다. 길게 쓰기보다 **한 줄에 하나의 판단 기준**을 적습니다.
- 같은 규칙을 여러 파일에 반복하지 않습니다. 금지 사항은 `core/constraints.md`에 모읍니다.
- 지침 문서의 코드는 3줄 이하로 씁니다. 더 긴 예시, 명령 모음, 폴더 트리, 템플릿은 표나 목록으로 바꿉니다.

## 검증 현황

| 항목 | 상태 |
| --- | --- |
| 규칙 자동 로드 (`core` 항상, `guides`·`records`는 경로별) | 확인함. 새 세션에서 `src/app`, `app.json`, `docs/decisions` 파일을 읽을 때 해당 규칙만 추가 로드됨 |
| 스킬 5개 | 확인함. 읽기 전용으로 실행해 양식대로 동작하고 파일을 바꾸지 않음 |
| Expo SDK 58 문서 대조 (`native.md`, `testing.md`) | 확인함 |
| `api.md`, `testing.md`, `issues.md` 자동 로드 | 미확인. 해당 경로에 파일이 생기면 확인 필요 |
| Codex의 `.agents/skills` 인식 | 미확인 |
| Live Activity 파일의 `@expo/ui/swift-ui` import 허용 | 미확인. 문서 문장과 예제가 달라 `expo-widgets` 설치 후 빌드로 확인 필요 |
