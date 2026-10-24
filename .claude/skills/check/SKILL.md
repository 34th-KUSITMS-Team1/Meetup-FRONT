---
name: check
description: 변경된 코드가 이 프로젝트 규칙(Expo/RN 제약, 폴더 구조, 딥링크·Live Activity, 데이터 패칭)을 지키는지 읽기 전용으로 점검합니다. 일반 버그 리뷰는 기본 /code-review를 사용합니다.
argument-hint: "[파일 경로 | ref]"
---

# 프로젝트 규칙 점검

코드를 수정하지 않습니다. 기본 `/code-review`가 보는 일반 버그보다 **이 프로젝트 규칙 위반**에 집중합니다.

## 현재 변경

- 파일: !`git status --short`
- 통계: !`git diff HEAD --stat`

## 순서

1. 범위를 정합니다. 인자가 파일이면 그 파일, ref면 `git diff <ref>...HEAD`, 없으면 `git diff HEAD`.
2. 변경 파일 경로에 맞는 규칙만 읽습니다. 읽지 않은 규칙을 근거로 단정하지 않습니다.
   - `src/**` → `.claude/rules/guides/structure.md`, `.claude/rules/guides/style.md`
   - 화면/컴포넌트/폼 → `.claude/rules/guides/ui.md`
   - `src/api`, `src/hooks`, 서버 데이터를 쓰는 화면 → `.claude/rules/guides/api.md`
   - `src/live-activities`, `transfer`·`delivery` 폴더, `src/app/order`, `app.json` → `.claude/rules/guides/native.md`
   - 의존성, 네이티브 설정 → `.claude/rules/core/constraints.md`
3. 아래 항목을 확인하고 실제 위반만 보고합니다.

## 점검 항목

- `src/app`에 라우트가 아닌 파일, 금지된 폴더(`src/pages`, `src/screens`, `src/features` 등)
- 도메인 폴더(`goal`, `order`, `transfer`, `delivery`, `receipt`) 밖에 도메인 코드를 둔 경우
- `src/live-activities` 파일의 import, hook, 함수 밖 상수 사용
- Live Activity를 `getInstances()` 확인 없이 다시 `start`
- 외부 앱 딥링크의 scheme 미선언(`LSApplicationQueriesSchemes`, Android `queries`), 앱 미설치 fallback 누락
- 딥링크 대상 라우트 경로를 바꿨는데 링크를 만드는 쪽(`start(props, url)` 등)은 그대로인 경우
- 등록된 스펙 문서 없이 추측한 endpoint/request/response
- 서버 데이터를 `useState` + `useEffect`로 fetch, mutation 후 관련 query invalidate 누락
- 도메인 폴더 간 import
- `ios/`, `android/` 직접 수정, `pnpm exec expo install` 없이 추가한 의존성
- 네이티브 모듈 추가 시 development build 필요 안내 누락
- 웹 전용 API/utility, `TouchableOpacity`, 긴 목록의 `ScrollView` + `map`
- `Pressable`의 `accessibilityRole`, 아이콘 버튼의 `accessibilityLabel` 누락
- 데이터 화면의 loading/empty/error 상태 누락

## 출력

- `위반`과 `확인하지 못한 것` 두 섹션으로 나눕니다.
- 위반마다 `파일:라인`, 심각도(높음/중간/낮음), 근거 규칙 파일과 규칙 한 줄, 문제와 영향, 고치는 방법을 적고 심각도 순으로 정렬합니다.
- 위반이 없으면 "위반 없음"과 확인하지 못한 항목만 적습니다.
- 칭찬이나 요약은 쓰지 않습니다.
