---
paths:
  - "src/**/*.{ts,tsx}"
  - "tailwind.config.js"
---

# 코드 스타일

## TypeScript

- 폼 값과 도메인 데이터(목표, 주문, 영수증 등)에는 명시적인 타입을 둡니다.
- `any` 대신 형태를 모르면 `unknown`을 씁니다.
- 타입은 재사용되기 전까지 쓰는 곳 근처에 둡니다. 화면에서 쓰지 않는 필드까지 미리 만들지 않습니다.

## React Native

- 함수 컴포넌트만 씁니다.
- `div`, `span`, `button` 같은 웹 요소 대신 `View`, `Text`, `Pressable`을 씁니다. 문자열은 반드시 `Text` 안에 둡니다.
- 컴포넌트는 재사용되거나 복잡해진 뒤에 분리합니다.
- React Compiler가 켜져 있으므로 `useMemo`/`useCallback`/`memo`를 습관적으로 넣지 않습니다.
- 이미지는 `react-native`의 `Image` 대신 `expo-image`를 씁니다.
- 여러 화면이 공유하는 클라이언트 상태가 필요해지면 확인을 받고 Zustand를 도입해 `src/stores`에 둡니다. 한 화면 안의 상태는 `useState`로 충분합니다.

## 네이밍

| 대상 | 규칙 | 예 |
| --- | --- | --- |
| 컴포넌트 파일·함수 | PascalCase | `ReceiptCard.tsx`, `export const ReceiptCard` |
| props 타입 | `{컴포넌트명}Props` | `ReceiptCardProps` |
| 훅 | `use` + camelCase | `useAppReturn.ts` |
| 라우트 | Expo Router 규칙 | `[id].tsx`, `_layout.tsx` |
| 테스트 | 대상 옆 `.test.ts(x)` | `ReceiptCard.test.tsx` |

## 스타일링

- NativeWind `className`을 씁니다. `StyleSheet`/inline `style`은 애니메이션 값, 계산된 값처럼 className으로 표현할 수 없을 때만 씁니다.
- `hover:`, `grid`, `cursor-*` 같은 웹 전용 utility는 네이티브에서 동작하지 않으므로 쓰지 않습니다.
- 색상·간격 토큰은 `tailwind.config.js`의 `theme.extend`에 정의하고 씁니다. 아직 토큰이 없으므로, 디자인 시안이 나오기 전에는 Tailwind 기본 팔레트를 쓰고 같은 값을 여러 곳에 반복하게 되면 토큰 추가를 제안합니다.
