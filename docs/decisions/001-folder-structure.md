# 001. 폴더 구조: 타입별 + 도메인 하위 폴더

## 상태

- Accepted

## 맥락

Expo Router 앱의 폴더 구조를 정해야 합니다. MVP 도메인(목표, 주문, 송금, 배달, 영수증)이 명확하고, iOS Live Activity(`expo-widgets`)는 격리된 런타임에서 실행되어 앱 코드를 import할 수 없습니다.

## 결정

Expo 기본 템플릿의 타입별 구조(`src/app`, `components`, `hooks` 등)를 쓰고, 그 아래를 도메인 폴더로 나눕니다. Live Activity 레이아웃은 `src/live-activities`에 분리합니다.

## 근거

- SDK 55 이후 Expo 기본 템플릿과 같아 문서·도구 가정과 맞습니다.
- 도메인 하위 폴더만으로 MVP 규모의 응집도는 충분합니다.
- `'widget'` directive 파일은 import가 불가능하므로 별도 폴더로 두어 실수로 앱 코드와 섞이지 않게 합니다.

## 대안

- 기능별 구조(`src/features/*`): MVP 규모에서는 폴더 깊이가 과합니다.
- FSD: Expo Router와 계층이 겹치고, FSD의 `widgets` 이름이 iOS 위젯과 혼동됩니다.

## 영향

- 배치 기준은 `.claude/rules/guides/structure.md`, Live Activity 제약은 `.claude/rules/guides/native.md`를 따릅니다.
- 도메인이 늘어 타입별 폴더 간 이동이 잦아지면 기능별 구조로 재검토합니다.
