---
name: test
description: 컴포넌트, 훅, 유틸의 테스트를 jest-expo + React Native Testing Library로 작성하고 실행합니다. 테스트 도구가 없으면 설치를 먼저 제안합니다.
argument-hint: "[테스트할 파일 경로]"
---

# 테스트 작성

## 준비 상태

- jest-expo 설치 여부: !`grep -c '"jest-expo"' package.json || true`
- test 스크립트: !`grep -c '"test":' package.json || true`

위 값이 `0`이면 테스트를 작성하지 않습니다. `.claude/rules/guides/testing.md`의 설치 명령과 `package.json` 변경을 제안하고, 확인을 받은 뒤 설치부터 진행합니다.

## 순서

1. `.claude/rules/guides/testing.md`를 읽습니다.
2. 대상 파일을 읽고 사용자가 관찰할 수 있는 동작을 정리합니다.

| 대상 | 테스트할 것 |
| --- | --- |
| 컴포넌트 | props별 렌더링, 누르기/입력, 접근성 label |
| 라우트 화면 | 파라미터별 화면, 이동 |
| 훅 | query/mutation 상태, 파라미터, 앱 복귀(`AppState`) 같은 부수 효과 |
| 유틸 | 입력/출력, 경계값 |

3. 파일 위치를 정합니다.

| 대상 | 테스트 파일 |
| --- | --- |
| `src/components/receipt/ReceiptCard/ReceiptCard.tsx` | 같은 폴더의 `ReceiptCard.test.tsx` |
| `src/hooks/transfer/useAppReturn.ts` | 같은 폴더의 `useAppReturn.test.ts` |
| `src/app/order/[id]/tracking.tsx` | `__tests__/order/tracking.test.tsx` |

4. `pnpm test <테스트 파일>`로 그 파일만 먼저 실행합니다. 실패하면 테스트와 코드 중 어느 쪽이 틀렸는지 확인하고 고칩니다.

## 지키는 것

- `@testing-library/react-native`만 사용합니다. `react-test-renderer`, DOM matcher는 쓰지 않습니다.
- 쿼리 우선순위: `getByRole` → `getByLabelText` → `getByText` → `getByTestId`.
- 비동기 UI는 `findBy*` 또는 `waitFor`를 사용합니다.
- Expo 모듈은 `jest-expo` 기본 mock을 먼저 쓰고, 부족할 때만 `jest.mock`을 추가합니다. `expo-widgets`, `expo-linking`처럼 네이티브 동작이 핵심인 부분은 mock 호출 여부만 검증하고, 실제 동작은 기기에서 확인할 항목으로 남깁니다.
- `it` 하나에 동작 하나만 검증합니다. className이나 style 값은 검증하지 않습니다.
- 실패하는 테스트를 `skip`으로 숨기지 않습니다.
