---
paths:
  - "**/*.test.{ts,tsx}"
  - "__tests__/**/*"
---

# 테스트

## 설치 (미설치)

- 첫 테스트 작업에서 확인을 받고 `jest-expo`, `jest`, `@types/jest`, `@testing-library/react-native`를 `pnpm exec expo install <패키지들> -- --dev`로 설치합니다.
- `package.json`에 `"jest": { "preset": "jest-expo" }`와 `"test": "jest"` 스크립트를 추가합니다. SDK 58의 `jest-expo`는 Jest 30이 필요합니다.
- 문서: https://docs.expo.dev/develop/unit-testing/

## 위치

- 컴포넌트, 훅, 유틸 테스트는 대상 파일 옆에 둡니다.
- 라우트 화면 테스트는 `src/app`이 아니라 루트 `__tests__`에 두고 `@/app/...`로 import합니다.
- E2E 도구는 정하지 않았습니다. 도입하면 decision 문서로 남깁니다.

## 도구

- Jest + `jest-expo` preset(Expo 모듈 mock 포함), `@testing-library/react-native`를 씁니다.
- `react-test-renderer`(React 19 미지원), Vitest, jsdom, happy-dom은 쓰지 않습니다.

## 무엇을 테스트하나

- 회귀 가능성이 있는 사용자 동작: 누르기, 입력, 화면 전환, loading/empty/error 표시
- 폼: 검증 메시지와 제출 가능 여부
- 앱 복귀(`AppState`) 같은 부수 효과가 있는 훅
- API 훅: query 성공/실패 상태, mutation 후 관련 query invalidate
- 인터랙티브 UI를 snapshot만으로 테스트하지 않습니다. className, 구현 세부사항은 검증하지 않습니다.
