# expo install의 --dev 전달이 pnpm 12에서 잘못 동작함

## 무엇이 다른지

- 기대: `pnpm exec expo install husky -- --dev`로 husky가 `devDependencies`에 추가됨
- 실제: husky가 `dependencies`에 들어가고, 일부 패키지(`react-native-screens`, `react-native-web`, `react-native-worklets` 등)가 `node_modules`에서 제거되어 `expo` 실행 파일을 찾지 못함

## 어디서 확인했는지

- 2026-10-24 husky 설치 중 재현. 환경: pnpm 12.5.1, Expo CLI 58.1.13
- `git checkout -- package.json pnpm-lock.yaml` 후 `pnpm install`로 복구함
- `pnpm add --save-dev husky`는 정상 동작함 (lock 파일 패키지 목록에서 husky 1개만 추가)

## 왜 지금 우리 코드에서 고치면 안 되는지

Expo CLI가 pnpm에 인자를 넘기는 방식의 문제라 프로젝트 코드로 고칠 수 없습니다.

## 언제/누가 해결해야 하는지

Expo CLI 쪽 수정이 필요합니다. 그 전까지 Expo SDK와 무관한 개발 도구는 `pnpm add --save-dev`로 설치합니다(`.claude/rules/core/constraints.md`). Expo SDK 업그레이드 때 다시 확인합니다.
