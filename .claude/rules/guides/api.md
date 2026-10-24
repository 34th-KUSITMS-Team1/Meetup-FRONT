---
paths:
  - "src/api/**/*"
  - "src/hooks/**/*"
---

# API / 데이터 패칭

## API 스펙

| 항목 | 값 |
| --- | --- |
| 스펙 문서 | (추후 입력) |
| Base URL 환경 변수 | (추후 입력) |
| 인증 방식 | (추후 입력) |

- endpoint, request, response는 위 스펙 문서로만 확인합니다.
- 스펙 문서가 `(추후 입력)`이거나 문서에 없는 API면 추측하지 않고 사용자에게 스펙을 요청합니다.
- 스펙과 실제 응답이 다르면 `.claude/rules/records/issues.md` 기준으로 기록합니다.

## 작업 순서

1. 스펙 문서에서 endpoint와 request/response를 확인합니다.
2. 요청 함수를 `src/api/{도메인}.ts`(예: `orders.ts`, `goals.ts`)에 만듭니다.
3. TanStack Query 훅으로 감싸 `src/hooks/{도메인}`에 둡니다.
4. 화면은 훅만 사용합니다.

## 서버 상태

- 서버 데이터는 TanStack Query v5로 다룹니다. 아직 미설치라 첫 API 작업에서 확인 후 도입합니다.
- `useState` + `useEffect`로 서버 데이터를 직접 fetch하지 않습니다. 서버 데이터를 Zustand에 복사하지 않습니다.
- query key는 도메인 기준으로 만들고 훅 또는 API 파일 근처에 둡니다.
- 생성, 수정, 삭제, 상태 변경은 mutation을 쓰고, 성공하면 관련 query를 invalidate합니다.
- 앱 복귀 시 다시 불러와야 하면 `focusManager` + `AppState`, 네트워크 상태 연동은 `onlineManager`를 설정합니다. 웹과 달리 자동으로 되지 않습니다.

## API 계층

- 요청 함수는 React 컴포넌트를 import하지 않고, 컴포넌트는 endpoint 경로를 직접 알지 않습니다.
- request/response 타입은 지금 화면에 필요한 만큼 명시적으로 둡니다.
- API client가 생기면 base URL과 인증 처리를 한곳에 모읍니다.
- base URL 같은 환경값은 `EXPO_PUBLIC_` 접두사 환경 변수로 읽습니다. 비밀 값은 앱 번들에 넣지 않습니다.
- 토큰은 `AsyncStorage`가 아니라 `expo-secure-store`에 저장합니다.
