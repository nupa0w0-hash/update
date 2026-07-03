# Eros Tower v1.1.65

- 날짜: 2026-07-03
- 배포 버전: `1.1.65`
- 호환 업데이트 채널: `4.0.16`
- 백업: `D:\리수작업\에로스 타워\백업\ErosTower_before_v1.1.65_post_pipeline_session_guard_hotfix_20260703-235329`

## 문제 증상

- 1.1.64에서 세션 읽기 guard가 걸릴 때 `세션 읽기 의심 상태라 커밋을 건너뜁니다` 안내가 뜨고, 후처리 번역 에이전트까지 실행되지 않았다.
- 진단 파일 `eros-tower-diagnostic-2026-07-03_14-51-16-471Z.json` 기준 최신 실행은 `status: guarded-no-state-commit`, `postPipelineResult: null`, `commitReason: session-guard`였다.
- 이 때문에 메인 모델의 중국어 원문 출력이 번역되지 않고 그대로 반환되었다.

## 원인

- `afterRequest`에서 `shouldBlockMemoryMutation(sessionSync)`가 참이면 즉시 반환했다.
- 세션 guard의 의도는 장기기억/관리상태 커밋 같은 상태 mutation을 막는 것이었지만, 코드상으로는 post pipeline 전체가 guard 뒤에 있어 번역/후처리도 같이 막혔다.

## 핵심 변경

- 후처리 에이전트, 번역 에이전트, 출력 정리 정규식은 세션 guard보다 먼저 실행한다.
- 세션 읽기 의심 상태에서는 관리상태 커밋과 기억 재정렬만 건너뛰고, 후처리된 최종 출력은 그대로 반환한다.
- guarded run log에도 `postPipelineResult`와 `qualityRegex`를 남겨 후처리 실행 여부를 진단할 수 있게 했다.
- 사용자 안내 문구를 `관리상태 커밋만 건너뜁니다 / 후처리 번역·출력 정리는 적용됨`으로 바꿨다.

## 수정 파일

- `ErosTower.v1.update.js`
- `☸에로스 타워.js`
- `ErosTower.update.js`
- `패치로그/2026-07-03_ErosTower_v1.1.65.md`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- 헤더 확인: v1 `//@version 1.1.65`, compat `//@version 4.0.16`
- 본문 동기화 확인: v1과 compat는 상단 5줄 제외 동일
- 코드 순서 확인: `runPostPipeline`과 `applyAdaptiveQualityOutput`가 session guard 이전에 실행됨

## 배포 후 확인

- v1 raw URL: 배포 후 확인 예정
- compat raw URL: 배포 후 확인 예정
