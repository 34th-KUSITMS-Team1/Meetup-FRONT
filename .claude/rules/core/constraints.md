# 금지/제약

## 패키지

- `pnpm`만 사용합니다. `npm`, `yarn`, `bun`, `npx` 대신 `pnpm exec` / `pnpm dlx`를 씁니다.
- 의존성은 `pnpm add`가 아니라 `pnpm exec expo install <pkg>`로 추가합니다. 추가 전에 사용자 확인을 받습니다.
- 서드파티 라이브러리보다 Expo 모듈을 우선합니다.
- EAS CLI는 `pnpm dlx eas-cli <command>`로 실행합니다.

## Expo / 네이티브

- `ios/`, `android/`는 생성물입니다. 직접 만들거나 수정하지 않고 `app.json`과 config plugin으로 설정합니다.
- 네이티브 코드가 있는 라이브러리는 Expo Go에서 동작하지 않습니다. 추가하면 development build가 필요하다고 알립니다.
- Expo/React Native API는 기억이 아니라 SDK 58 문서로 확인합니다.

## NativeWind / Tailwind

- NativeWind v4 + Tailwind CSS v3 조합입니다. `tailwind.config.js`, `global.css`, `babel.config.js`, `metro.config.js`가 함께 동작합니다.
- Tailwind v4로 올리거나 `@tailwindcss/*` 패키지를 추가하지 않습니다.

## API / 데이터 패칭

- endpoint, request, response는 `.claude/rules/guides/api.md`에 등록된 스펙 문서로만 확인합니다. 등록되지 않았으면 추측하지 말고 사용자에게 요청합니다.
- 서버 데이터는 TanStack Query로 다룹니다. 화면이나 컴포넌트에서 `useState` + `useEffect`로 직접 fetch하지 않습니다.
