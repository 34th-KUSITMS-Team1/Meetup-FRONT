---
paths:
  - "src/live-activities/**/*"
  - "src/**/transfer/**/*"
  - "src/**/delivery/**/*"
  - "src/app/order/**/*"
  - "src/app/+native-intent.tsx"
  - "app.json"
  - "package.json"
---

# 네이티브 기능 규칙 (딥링크, Live Activity)

Expo SDK 58 문서 기준입니다. 구현 전에 아래 문서를 다시 확인합니다.

- 다른 앱 열기: https://docs.expo.dev/linking/into-other-apps.md
- 앱으로 들어오는 링크: https://docs.expo.dev/linking/overview.md, https://docs.expo.dev/linking/into-your-app.md
- 들어오는 링크 재작성: https://docs.expo.dev/router/advanced/native-intent.md
- Live Activity / Dynamic Island: https://docs.expo.dev/versions/v58.0.0/sdk/widgets.md

## 공통

- 아래 기능은 Expo Go에서 검증할 수 없습니다. development build에서 확인하고, 확인하지 못했다면 보고합니다.
- 네이티브 설정은 `app.json`과 config plugin으로만 합니다. `ios/`, `android/`를 수정하지 않습니다.

## 다른 앱으로 나가는 딥링크 (송금 앱 이동)

- `expo-linking`의 `Linking.openURL` 또는 Expo Router `Link`로 엽니다.
- 송금 앱의 URL scheme과 파라미터는 해당 앱의 공식 문서로 확인합니다. 추측해서 하드코딩하지 않습니다.
- `Linking.canOpenURL`을 쓰려면 iOS는 `app.json`의 `ios.infoPlist.LSApplicationQueriesSchemes`에 대상 scheme을 선언해야 합니다. 없으면 설치되어 있어도 `false`가 나올 수 있습니다.
- Android 11+는 `AndroidManifest.xml`의 `queries` 선언이 필요하며 config plugin(`config.modResults.manifest.queries`)으로 추가합니다.
- 앱이 설치되지 않은 경우의 fallback(스토어 이동, 안내)을 항상 둡니다.
- 송금 성공 여부는 서비스가 직접 확인하지 않습니다(기획 기준). 앱 복귀는 `AppState`가 `active`로 바뀌는 시점에 감지하고, 사용자 확인("송금 완료했어요")을 받은 뒤 다음 상태로 넘어갑니다.

## 앱으로 들어오는 딥링크

- Expo Router는 모든 라우트에 딥링크를 자동으로 제공합니다. 라우트 경로가 곧 딥링크 경로이므로 수동 라우팅 테이블을 만들지 않습니다.
- scheme은 `app.json`의 `scheme`입니다. 변경하면 development build를 다시 만들어야 합니다.
- 외부 형식의 링크를 앱 경로로 바꿔야 할 때만 `src/app/+native-intent.tsx`를 사용합니다.
- 앱 안에서 돌아올 URL을 만들 때는 `Linking.createURL`을 사용합니다 (Expo Go에서는 `exp://`로 생성됨).
- 앱 미설치 상태까지 고려해야 하면 custom scheme 대신 iOS Universal Links / Android App Links를 검토하고 decision 문서로 남깁니다.

## Live Activity / Dynamic Island (`expo-widgets`)

- iOS 전용입니다. Android에는 Live Activity가 없으므로 대체 UX(알림 등)를 별도로 정합니다.
- prebuild에 `ios.bundleIdentifier` 또는 plugin의 `groupIdentifier`가 필요합니다.
- Live Activity는 plugin `widgets[]`에 등록하지 않습니다. 이름만 `createLiveActivity(name, component)`와 맞춥니다.
- 레이아웃 컴포넌트는 `'widget'` directive로 시작하고 `@expo/ui/swift-ui` 컴포넌트만 사용합니다.
  - React hook, state, context, async, import를 쓸 수 없습니다. `View`/`Text`도 사용할 수 없습니다.
  - 함수 밖의 상수는 런타임에 없습니다. 상수는 함수 안에 두거나 props로 전달합니다.
  - 따라서 앱 코드(`src/components`, `src/hooks` 등)를 import하는 구조를 만들지 않습니다.
- 영역: Lock Screen(`banner`, `bannerSmall`), Dynamic Island compact(`compactLeading`, `compactTrailing`), minimal(`minimal`), expanded(`expandedLeading`, `expandedTrailing`, `expandedCenter`, `expandedBottom`).
- 시작/갱신/종료: `factory.start(props, url?)` → `instance.update(props)` → `instance.end(dismissalPolicy?)`.
  - `start`의 `url`에 앱 라우트 딥링크를 넘겨 탭 시 해당 화면으로 이동시킵니다.
  - 앱 재시작 후에는 `factory.getInstances()`로 실행 중인 activity를 복구합니다. 중복 `start`하지 않습니다.
- 서버 push로 갱신하려면 `enablePushNotifications: true`가 필요합니다. API 스펙이 정해지기 전까지는 앱에서 `update`로만 갱신합니다.
