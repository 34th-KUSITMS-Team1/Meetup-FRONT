---
paths:
  - "src/app/**/*.tsx"
  - "src/components/**/*.tsx"
---

# UI

## 무엇으로 만들지

1. React Native 기본 컴포넌트와 설치된 Expo 모듈(`expo-image`, `expo-symbols`, `@expo/ui`, `expo-glass-effect`)
2. `src/components/common`에 있는 컴포넌트
3. 위로 안 될 때만 새 컴포넌트

아이콘은 `expo-symbols`를 씁니다. 새 아이콘·UI 라이브러리는 확인 후 도입합니다.

## 화면 기본값

- 화면 루트에서 safe area를 처리합니다(`react-native-safe-area-context`).
- 길거나 개수가 정해지지 않은 목록은 `FlatList`/`SectionList`를 씁니다. `ScrollView` + `map`은 짧고 고정된 목록에만 씁니다.
- 입력 화면은 키보드가 입력창을 가리지 않게 합니다.
- 데이터를 보여주는 화면에는 loading, empty, error 상태를 둡니다.
- 다크 모드(`userInterfaceStyle: automatic`)에서 글자와 배경 대비를 확인합니다(`dark:` variant).
- 버튼 같은 액션은 영향을 주는 데이터 가까이에 둡니다.
- `window`, `document`, `localStorage` 같은 웹 API를 쓰지 않습니다.

## 접근성

- 누를 수 있는 요소는 `Pressable` + `accessibilityRole`을 씁니다. `TouchableOpacity` 같은 레거시 Touchable은 쓰지 않습니다.
- 터치 영역은 최소 44x44pt입니다. 작은 아이콘은 `hitSlop`으로 넓힙니다.
- 아이콘만 있는 버튼과 label이 안 보이는 input에는 `accessibilityLabel`을 둡니다.
- 상태를 색상만으로 표현하지 않습니다.

## 컴포넌트 작성

`src/components/common`에 첫 공용 컴포넌트가 생기면 그 파일을 기준 예시로 삼습니다. 그 전까지는 아래를 따릅니다.

- `className` prop을 열어 밖에서 스타일을 덧붙일 수 있게 합니다.
- 나머지 props는 `...props`로 넘깁니다. React 19에서는 `ref`도 props로 전달됩니다.
- 시각 변형은 boolean props 여러 개 대신 `variant` 하나와 클래스 맵 객체로 관리합니다. variant 라이브러리는 확인 후 도입합니다.
- 상태는 부모가 갖고 컴포넌트는 `value` + `onValueChange`로 받습니다.
- 구조는 컴포넌트가 책임지고 icon, action, children 같은 위치만 props로 주입받습니다.
- 반복 UI는 데이터 배열 + `map`으로 렌더링합니다.

## 폼

- 목표 금액, 계좌, 주소 같은 입력 폼은 React Hook Form + Zod를 씁니다. 아직 미설치라 첫 폼 작업에서 확인 후 도입합니다.
- `TextInput`은 `Controller`로 연결합니다. schema는 재사용되기 전까지 폼 근처에 둡니다.
- 금액 입력은 숫자 키패드(`keyboardType="number-pad"`)를 쓰고, 화면에는 천 단위 구분을 보여줍니다.
