# Eros Tower v1.1.28

## 날짜

2026-06-30

## 문제 증상과 원인

- v1.1.27에서 메인 주입 auto-cap은 제거했지만, 디버그/Run Log/설정 export 쪽에 `autoCap` 관련 표시 잔재가 남아 있었다.
- 디버그 helper의 기본 주입 예산 fallback에 `22000`이 남아 있어, 실제 기본값 `0 = 무제한`과 혼동될 수 있었다.
- 업데이트 감지를 확실히 하기 위해 플러그인 메타 버전을 `1.1.28`로 올렸다.

## 수정한 파일

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## 핵심 변경 내용

- `//@display-name`, `//@version`, 내부 `VERSION`, 플러그인 라벨, 메인 주입 제목을 `1.1.28`로 갱신했다.
- Run Log와 진단 export에서 더 이상 auto-cap 정보를 노출하지 않도록 잔재를 제거했다.
- 디버그 helper의 `buildMainBriefing` 기본 budget을 `22000` 대신 `0`으로 맞췄다.
- 과거 저장값 `injectionBudget: 22000` + `autoCapEnabled: true` 조합을 `0 = 무제한`으로 마이그레이션하는 호환 조건은 유지했다.
- `et_injection_budget`은 명시적으로 양수 지정했을 때만 수동 cap으로 작동한다.

## 검증

- `node --check update_repo\ErosTower.v1.update.js` 통과
- `node --check 완성본\에로스 타워 v0.3\eros-tower-v0.3.js` 통과
- `node 완성본\에로스 타워 v0.3\tests\smoke-v0.3.js` 통과
- `node 완성본\에로스 타워 v0.3\tests\session-sim-v0.3.js 20` 통과
- `node 완성본\에로스 타워 v0.3\tests\regression-v0.3.js` 통과

## 차후 점검할 위험 요소

- GitHub raw URL은 캐시 때문에 업데이트 표시가 몇 분 늦을 수 있다.
- 사용자가 `et_injection_budget`에 양수를 직접 넣은 경우에는 의도된 수동 cap으로 유지된다.
