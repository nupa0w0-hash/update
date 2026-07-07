# Eros Tower 1.1.77 Release

- 배포일: 2026-07-07
- 기준 버전: 1.1.76 -> 1.1.77

## 변경 사항

- 원문 병행 번역 결과가 최종 sanitize 단계에서 초안 누출로 오판되어 앞 원문/번역 쌍이 잘릴 수 있는 문제를 수정했다.
- 원문 병행 번역으로 생성된 결과에는 `removeBilingualDraftLeak()`를 적용하지 않도록 했다.
- 일반 출력의 내부 태그 제거와 초안 누출 제거는 유지했다.
- 보호 기준은 언어가 아니라 원문 병행 번역 실행 경로이므로 영어, 일본어, 중국어 등 원문 언어를 가리지 않고 보존된다.

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check -- ErosTower.v1.update.js ErosTower.update.js "☸에로스 타워.js"`
- 영어/일본어/중국어 원문 병행 보존 VM 테스트 통과
- 일반 출력의 영어 초안 제거 대조 테스트 통과
