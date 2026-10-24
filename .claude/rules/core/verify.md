# 검증

주장을 증명할 수 있는 가장 작은 검증을 고릅니다.

| 변경 | 검증 |
| --- | --- |
| 문서만 | 변경 파일 확인 |
| 코드 | `pnpm lint`, `pnpm exec tsc --noEmit` 항상 실행 |
| 테스트가 있는 컴포넌트/훅 | `pnpm test <경로>` 추가 |
| 의존성, `app.json`, config plugin | `pnpm dlx expo-doctor` 추가 |
| 네이티브 모듈 추가 | development build 필요(`pnpm exec expo run:ios`, `pnpm dlx eas-cli build --profile development`)라고 보고 |
| 화면 동작 | 시뮬레이터/기기에서 보지 못했다면 확인하지 못한 항목으로 보고 |

## 보고

- 무엇을 바꿨는지
- 무엇으로 검증했는지 (iOS/Android 중 어디서 확인했는지 포함)
- 확인하지 못한 것과 이유
- 명령이 환경 문제로 실패하면 원인과 그다음으로 믿을 수 있는 근거
