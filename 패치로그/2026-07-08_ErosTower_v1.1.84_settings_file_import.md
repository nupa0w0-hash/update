# Eros Tower 1.1.84 - Settings File Import

## 변경

- `전체 설정 백업 / 불러오기` 섹션에 JSON 파일 선택 입력을 추가했다.
- `파일에서 설정 불러오기` 버튼을 추가해 로컬 JSON 파일에서 바로 전체 설정을 복원할 수 있게 했다.
- 파일 import와 붙여넣기 import가 같은 `importSettingsBackupPackage -> Storage.config` 검증/저장 경로를 사용하도록 정리했다.
- `File.text()`를 지원하지 않는 WebView를 위해 `FileReader` fallback을 추가했다.

## 유지한 것

- 채팅별 관리상태, 장기기억 원장, Run Log, 스냅샷, 임베딩 캐시는 설정 import 대상이 아니다.
- 로어/상태/기억/에이전트 실행 경로는 변경하지 않았다.

## 백업

- `D:\리수작업\에로스 타워\백업\before_settings_file_import_1.1.84_20260708-182708`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check ☸에로스 타워.js`
- v1 파일과 직접 설치 파일 SHA256 동일 확인
- v1/compat 본문 동기화 확인
- RisuAI 최소 mock 로드 확인: `☸에로스 타워 v1.1.84 loaded.`

## 배포

- GitHub 푸시 후 raw URL 확인 결과를 기록할 예정.
