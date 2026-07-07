# Eros Tower 원문 병행 출력 보존 수정

- 작업 시각: 2026-07-07
- 기준 버전: 1.1.76
- 배포 상태: 1.1.77 배포 반영
- 백업 위치: `D:\리수작업\에로스 타워\작업메모\backup_before_translation_parallel_sanitize_fix_20260707-083743`

## 원인

원문 병행 번역은 `원문 -> 번역 -> 원문 -> 번역` 형식으로 최종 출력을 구성한다.
하지만 후처리 이후 공통 `sanitizeFinalOutput()` 경로에서 `removeBilingualDraftLeak()`가 다시 실행되면서, 영어 원문으로 시작하는 정상 병행 출력이 "영어 초안 누출"로 오판될 수 있었다.

진단 패키지에서는 번역 에이전트가 id 0부터 번역을 반환했으나, 최종 preview가 중간 번역부터 시작했다. 따라서 모델이 처음 문단을 번역하지 않은 문제가 아니라, 플러그인 후처리 sanitize 경로에서 앞 원문/번역 쌍이 잘린 문제로 판단했다.

## 수정

- `runPostPipeline()`에서 번역 에이전트의 원문 병행 모드가 실제 결과를 만들었을 때 `preserveBilingualDraft` 플래그를 반환한다.
- `sanitizeFinalOutput(text, options)`에 `preserveBilingualDraft` 옵션을 추가했다.
- 원문 병행 결과에는 `removeBilingualDraftLeak()`를 적용하지 않는다.
- 일반 출력에는 기존 영어 드래프트 누출 제거 동작을 유지한다.
- 판정은 언어가 아니라 실행 모드 기준이다. 영어, 일본어, 중국어 원문 모두 같은 방식으로 보존된다.

## 변경 파일

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check -- ErosTower.v1.update.js ErosTower.update.js "☸에로스 타워.js"`
- 영어/일본어/중국어 원문 병행 출력 보존 VM 테스트 통과
- 일반 출력의 초반 영어 드래프트 제거 대조 테스트 통과
