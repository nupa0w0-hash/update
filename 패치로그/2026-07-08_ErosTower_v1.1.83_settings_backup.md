# Eros Tower 1.1.83 - Full Settings Backup

## 변경

- API / Provider 화면에 `전체 설정 백업 / 불러오기` 섹션을 추가했다.
- 현재 대시보드 화면의 설정값을 `eros-tower-settings` JSON으로 다운로드하거나 클립보드에 복사할 수 있게 했다.
- 붙여넣은 `eros-tower-settings` JSON으로 `Storage.config`를 교체하는 불러오기 기능을 추가했다.
- 백업에는 Provider registry/API Key, 에이전트 설정, 프롬프트 모드, 번역/고에양 모드, 임베딩, 참고 자료, 품질관리 설정을 포함한다.
- 채팅별 관리상태, 장기기억 원장, Run Log, 스냅샷, 임베딩 캐시, 사용량/쿼터 스냅샷은 설정 백업에서 제외한다.
- 마스킹된 `*****` API Key가 포함된 JSON은 가져오지 않도록 막았다.

## 이유

- 사용자가 Provider와 에이전트 모델, 프롬프트, 자료 주입 관련 설정을 다른 환경으로 옮기거나 보관할 수 있어야 한다.
- 기존 설정 저장 경로인 `readDashboardConfigFromUI -> configForStorage -> Storage.config`를 재사용해 런타임 상태/기억 데이터와 설정을 섞지 않도록 했다.

## 백업

- `D:\리수작업\에로스 타워\백업\before_settings_backup_feature_20260708-180118`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check ☸에로스 타워.js`
- RisuAI 최소 mock으로 플러그인 로드 확인: `☸에로스 타워 v1.1.83 loaded.`

## 배포

- GitHub `main` 배포 완료.
- 커밋: `ddf977c Release Eros Tower 1.1.83`
- v1 raw URL 확인: `//@display-name ☸Eros Tower 1.1.83` / `//@version 1.1.83`
- compat raw URL 확인: `//@display-name ☸Eros Tower 1.1.83` / `//@version 4.0.25`
