## Meetup-FRONT 에이전트 지침

이 파일은 AI 코딩 에이전트의 진입점입니다. Expo + React Native 모바일 앱이며, 모바일 우선 패턴, 성능, iOS/Android 호환성을 우선합니다.

### 사고 방식

- 사용자가 다르게 요청하지 않으면 한국어로 사고하고 응답합니다.
- 코드 식별자(변수/함수/파일명), 터미널 명령어, 라이브러리·패키지 고유명사를 제외하고 영어 문장을 한국어 응답에 섞지 않습니다.
- 한글이 터미널에서 깨져 보이면 파일 의도, 주변 코드, 기존 프로젝트 패턴을 기준으로 판단합니다.

### 기술 스택

| 항목 | 사용 |
| --- | --- |
| Framework | Expo SDK 58, React Native 0.88, React 19 |
| Routing | Expo Router (`src/app`, typed routes) |
| Language | TypeScript (strict) |
| Styling | NativeWind v4 + Tailwind CSS v3 (`tailwind.config.js`) |
| Server State | TanStack Query (미설치, 도입 예정) |
| Client State | Zustand (미설치, 필요 시 도입) |
| Test | jest-expo + `@testing-library/react-native` (미설치, 도입 예정) |
| Package Manager | pnpm |

### Expo는 자주 바뀝니다 — 학습 데이터를 믿지 않습니다

Expo는 SDK마다 breaking change가 있습니다. Expo, EAS, React Native API를 다루는 코드를 쓰기 전에:

1. `package.json`의 `expo` major 버전을 확인합니다.
2. 해당 버전 문서를 확인합니다: `https://docs.expo.dev/versions/v<major>.0.0/`
3. 그 밖의 내용은 https://docs.expo.dev/llms.txt 에서 필요한 페이지를 찾아 확인합니다. 기억으로 답하지 않습니다. 문서 URL 끝에 `.md`를 붙이면 markdown 원문을 받을 수 있습니다.

### 주요 명령어

| 명령 | 용도 |
| --- | --- |
| `pnpm start` | 개발 서버 (expo start) |
| `pnpm ios`, `pnpm android` | 플랫폼 지정 실행 |
| `pnpm lint` | expo lint |
| `pnpm exec tsc --noEmit` | 타입 체크 |
| `pnpm exec expo install <pkg>` | 의존성 추가 (`pnpm add` 대신 항상 사용) |
| `pnpm exec expo install --fix` | SDK 호환 버전으로 정리 |
| `pnpm exec expo-doctor` | 의존성/설정 진단 |

### 규칙 로딩

Claude Code는 `core/`를 항상 로드하고, `guides/`·`records/`는 각 파일의 `paths`에 맞는 파일을 다룰 때 자동으로 로드합니다. Codex 등 자동 로드가 없는 도구는 아래 표를 보고 직접 읽습니다.

#### 항상 읽기

| 파일 | 내용 |
| --- | --- |
| `.claude/rules/core/principles.md` | 추측하지 않기, 단순하게, 필요한 만큼만 수정 |
| `.claude/rules/core/constraints.md` | 패키지·네이티브·Tailwind 제약, API 스펙·데이터 패칭 기본 원칙 |
| `.claude/rules/core/workflow.md` | 작업 순서, 기록이 필요한 순간 |
| `.claude/rules/core/verify.md` | 변경별 검증 명령, 보고 형식 |

#### 작업할 때 읽기

| 작업 | 파일 |
| --- | --- |
| 파일 배치, 라우트, import | `.claude/rules/guides/structure.md` |
| TypeScript, React Native, 네이밍, 스타일링 | `.claude/rules/guides/style.md` |
| 화면, 컴포넌트, 폼 | `.claude/rules/guides/ui.md` |
| API 스펙, 요청 함수, 서버 상태 | `.claude/rules/guides/api.md` |
| 테스트 | `.claude/rules/guides/testing.md` |
| 딥링크, 다른 앱 열기, Live Activity/Dynamic Island | `.claude/rules/guides/native.md` |
| 구조·라이브러리 결정 기록 | `.claude/rules/records/adr.md` |
| 외부 요인 이슈 기록 | `.claude/rules/records/issues.md` |
| 커밋, PR | `commit`, `pr` 스킬 |

### Skills

| Skill | 용도 |
| --- | --- |
| `commit` | 커밋 컨벤션에 맞는 커밋 메시지와 커밋 단위 제안 |
| `pr` | PR 템플릿에 맞춘 제목과 본문 초안 작성 |
| `check` | 변경 코드의 프로젝트 규칙 위반 점검 (읽기 전용) |
| `tidy` | 동작 변경 없이 파일 위치·책임 분리를 구조 규칙에 맞게 정리 |
| `test` | jest-expo + React Native Testing Library 테스트 작성·실행 |

- 원본은 `.claude/skills/<name>/SKILL.md`이고, Codex는 `.agents/skills/<name>/SKILL.md`(원본을 읽도록 연결한 파일)로 같은 스킬을 씁니다.
- 일반 버그 리뷰는 Claude Code 기본 `/code-review`, 코드 단순화는 기본 `/simplify`를 사용합니다. 기본 명령이나 별칭과 같은 이름(`review`, `code-review`, `simplify` 등)으로 스킬을 만들지 않습니다(기본 명령을 덮어쓰거나 실행되지 않음).

### 금지 패턴

- 코드 관련 금지 사항은 `.claude/rules/core/constraints.md`에 모읍니다. 같은 규칙을 여러 파일에 반복하지 않습니다.
- 규칙, 스킬, README 등 지침 문서의 코드는 3줄 이하로만 씁니다. 그보다 긴 예시 코드, 명령 모음, 폴더 트리, 템플릿은 표, 목록, 인라인 코드로 바꿉니다. 꼭 필요하면 넣기 전에 사용자에게 이유를 설명하고 확인을 받습니다.
