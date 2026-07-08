# Eros Tower 1.1.85 - Settings Import Preserve Merge

## 증상

- 설정 불러오기 후 에이전트 온도, 모델, 켜짐/꺼짐, 최근 대화 수치가 기본값처럼 바뀌는 사례가 보고됨.

## 원인

- 1.1.83/1.1.84의 설정 불러오기는 가져온 JSON을 검증한 뒤 `Storage.config`를 통째로 교체했다.
- 최신 `eros-tower-settings` 전체 백업에는 `pipelineJson`이 포함되므로 정상이어야 하지만, 일부 필드가 빠진 JSON이나 오래된 설정 JSON을 불러오면 기존 `pipelineJson`이 사라질 수 있었다.
- 이 경우 다음 `getConfig()` 정규화에서 에이전트 설정이 기본 pipeline 값으로 돌아갈 수 있다.

## 변경

- 설정 import 시 `현재 저장 설정 + 가져온 설정`을 병합한 뒤 저장하도록 변경했다.
- 가져온 JSON에 있는 값은 그대로 덮어쓰되, 없는 값은 기존 설정을 보존한다.
- 따라서 `pipelineJson`이 빠진 partial/older JSON을 불러와도 기존 에이전트 켜짐/꺼짐, 모델, 온도, max tokens, 최근 대화 수치가 날아가지 않는다.

## 백업

- `D:\리수작업\에로스 타워\백업\before_settings_import_merge_fix_1.1.85_20260708-185055`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check ☸에로스 타워.js`
- v1/compat 본문 동기화 확인
- v1 파일과 직접 설치 파일 SHA256 동일 확인

## 배포

- GitHub `main` 배포 완료.
- 커밋: `8ae287f Release Eros Tower 1.1.85`
- v1 raw URL 확인: `//@display-name ☸Eros Tower 1.1.85` / `//@version 1.1.85`
- compat raw URL 확인: `//@display-name ☸Eros Tower 1.1.85` / `//@version 4.0.27`
