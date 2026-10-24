---
paths:
  - "src/**/*"
  - "tsconfig.json"
---

# 아키텍처 규칙

## 프로젝트 형태

Expo Router 기반 모바일 앱입니다. Expo 기본 템플릿의 타입별 구조(`src/app`, `components`, `hooks`, `constants`)를 따르고, 그 아래를 도메인 폴더로 나눕니다. `src/features` 같은 기능별 최상위 구조나 FSD는 도입하지 않습니다.

도메인: `goal`(목표 설정), `order`(매장·메뉴·장바구니·주문), `transfer`(송금 앱 이동·복귀), `delivery`(배달 추적), `receipt`(절제 영수증). 새 도메인은 기존 도메인으로 표현할 수 없을 때만 추가합니다.

## 파일 배치

| 위치 | 용도 |
| --- | --- |
| `src/app` | Expo Router 라우트만. 모든 파일이 화면이 되고 `_layout.tsx`가 navigator를 정의 |
| `src/api` | 도메인별 요청 함수 (`orders.ts`, `goals.ts`) |
| `src/components` | 재사용 UI 컴포넌트 (`common` 또는 `{domain}`) |
| `src/live-activities` | `expo-widgets` Live Activity 레이아웃 (`'widget'` directive 파일만) |
| `src/hooks` | 커스텀 훅 (`common` 또는 `{domain}`) |
| `src/stores` | 여러 화면이 공유하는 클라이언트 상태 (Zustand, 도입 시) |
| `src/lib` | 공통 유틸리티 |
| `src/constants` | 앱 전역 상수 |
| `src/types.ts` | 공통 타입 (도메인 전용이면 해당 도메인 파일 근처) |
| `assets` | 이미지, 폰트, 앱 아이콘 (`app.json`에서 참조) |
| `__tests__` | 라우트 화면 테스트 |

- 위 폴더는 처음 필요한 파일이 생길 때 만듭니다. 빈 폴더나 `.gitkeep`을 미리 만들지 않습니다.
- `src/pages`, `src/routes`, `src/layout`, `src/screens`는 만들지 않습니다. 라우트와 레이아웃은 `src/app`이 담당합니다.

## 라우트 (`src/app`)

- 라우트 파일은 화면 조립과 데이터 흐름만 담당하고, UI 조각은 `src/components`로 분리합니다.
- 라우트 파일은 `export default`로 화면 컴포넌트를 내보냅니다.
- 그룹은 `(group)`, 동적 세그먼트는 `[id].tsx`, 레이아웃은 `_layout.tsx`를 사용합니다.
- 네비게이션은 `expo-router`의 `Link`, `router`, `useLocalSearchParams`를 사용합니다.
- typed routes가 켜져 있으므로 경로 문자열을 타입 오류 없이 사용합니다.
- 컴포넌트, 훅, 유틸, 테스트 파일을 `src/app` 안에 두지 않습니다. Expo Router가 라우트로 취급합니다.
- 라우트 경로가 곧 딥링크 경로입니다(`order/[id]/tracking.tsx` → `meetupfront://order/123/tracking`). 딥링크 대상 화면은 경로를 바꾸면 Live Activity `url` 등 링크를 만드는 쪽도 함께 수정합니다.
- 문서: https://docs.expo.dev/router/introduction.md, https://docs.expo.dev/router/basics/core-concepts.md

MVP 기준 목표 라우트입니다. 화면은 실제로 만들 때 추가합니다. 모든 경로는 `src/app/` 기준입니다.

| 라우트 파일 | 화면 |
| --- | --- |
| `_layout.tsx` | 루트 레이아웃 |
| `(auth)/login.tsx` | 로그인 |
| `(tabs)/index.tsx` | 홈 / 목표 진행률 |
| `(tabs)/receipts.tsx` | 절제 영수증 목록 |
| `goal/new.tsx` | 목표·금액·계좌·주소 입력 |
| `store/[id].tsx` | 매장·메뉴 |
| `cart.tsx` | 장바구니 |
| `order/[id]/transfer.tsx` | 송금 앱 이동 → 복귀 확인 |
| `order/[id]/tracking.tsx` | 배달 추적 (Live Activity 탭 목적지) |

## Import 규칙

다른 폴더는 `@/` alias(`@/components/...`, `@/hooks/...`, `@/assets/...`)를 사용하고, 같은 폴더 안의 파일은 상대 import를 우선합니다.

## 경계

- 라우트는 컴포넌트와 훅을 조합합니다. 컴포넌트는 라우트를 import하지 않습니다.
- 데이터 흐름은 `src/api` → `src/hooks` → 화면 방향입니다. 세부 규칙은 `.claude/rules/guides/api.md`를 따릅니다.
- 도메인 폴더끼리 서로 import하지 않습니다. 둘 이상의 도메인이 쓰면 `common`으로 옮깁니다.
- `src/live-activities` 파일은 아무것도 import하지 않고(`@expo/ui/swift-ui` 제외), 다른 폴더도 레이아웃 컴포넌트를 렌더링용으로 import하지 않습니다. 시작/갱신/종료는 `src/hooks/delivery`의 훅이 담당합니다. 세부 규칙은 `.claude/rules/guides/native.md`를 따릅니다.

## 컴포넌트/훅 폴더 구조

| 위치 | 예시 |
| --- | --- |
| `src/components/common/{Name}/` | `Button/Button.tsx`, `Button/Button.test.tsx` |
| `src/components/{domain}/{Name}/` | `receipt/ReceiptCard/ReceiptCard.tsx` |
| `src/hooks/{common,domain}/` | `transfer/useAppReturn.ts`, `delivery/useDeliveryActivity.ts` |
| `src/live-activities/` | `DeliveryActivity.tsx` (`'widget'` directive, `createLiveActivity`) |

`{domain}`은 `goal`, `order`, `transfer`, `delivery`, `receipt` 중 하나입니다.

- 재사용 컴포넌트는 `src/components/{domain}/{ComponentName}/{ComponentName}.tsx`처럼 컴포넌트별 폴더에 두고 테스트를 co-locate합니다.
- 같은 도메인의 다른 컴포넌트를 참조할 때는 `../ComponentName` 상대 import를 사용합니다.
- 플랫폼별 구현이 필요하면 `Component.ios.tsx` / `Component.android.tsx` 확장자를 사용하고, 작은 차이는 `Platform.select`로 처리합니다.
