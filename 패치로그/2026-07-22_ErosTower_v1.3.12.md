# Eros Tower 1.3.12 장기기억 의미 분석·무결성·저장소 수명 복구

## 문제와 원인

- `chat_long_memory` 청크의 기존 cold-start는 모델 호출이 형식상 성공하면 실제 영속 상태가 비어 있어도 `extracted: true`와 `processedHashes`를 기록했다.
- 캐릭터 상태·관계·호감도·비밀 데이터가 생성되지 않았거나 나중에 사라져도 청크에서 파생 상태로 향하는 역방향 무결성 검사가 없었다.
- 기존 출력은 구조화 commit만 요구했기 때문에, 청크를 의미적으로 분석했는지와 단순히 빈 구조를 반환했는지를 구분할 수 없었다.
- 여러 청크가 한 번에 처리될 때 이전 청크에서 병합된 압축 상태가 다음 batch의 기준 상태로 제공되지 않았다.

## 핵심 변경

### 분석과 상태 병합 분리

- cold-start 출력에 commit과 별도의 versioned `analysis` 계약을 추가했다.
- 분석은 다음 여섯 범주를 모두 `found`, `none`, `uncertain` 중 하나로 판정한다.
  - `characterState`
  - `relationships`
  - `affection`
  - `secrets`
  - `events`
  - `continuity`
- `facts-found`는 발견 범주별 evidence-bearing observation과 대응하는 구조화 commit을 모두 요구한다.
- 정말 영속 정보가 없을 때만 모든 범주를 `none`으로 선언하고 `noDurableFacts: true` 및 빈 commit을 반환할 수 있다.

### 빈 결과 성공 처리 제거

- 분석이 영속 사실을 발견했는데 commit이 비어 있으면 `cold-start-analysis-without-state`로 실패한다.
- 분석 범주와 commit 레코드 종류가 맞지 않으면 `cold-start-commit-coverage-missing`으로 실패한다.
- 상태 병합 후 실제 파생 레코드를 찾지 못하면 `cold-start-merge-coverage-missing`으로 실패한다.
- 성공한 청크만 `processedHashes`와 `extracted: true`에 들어간다.

### 청크별 분석 manifest와 역방향 무결성

- `coldStart.analyses[]`에 청크별 분석 버전, 분석/병합 상태, 범주 판정, 관찰 근거, `noDurableFacts`, commit counts, 파생 레코드 ID를 저장한다.
- 청크 메타데이터에도 분석 버전, 분석 상태, 병합 상태, 결과 유형, 파생 레코드 수를 저장한다.
- 다음 실행에서 `found`로 판정된 범주의 파생 캐릭터·관계·비밀·사건 등이 사라졌으면 해당 청크만 자동 재대기한다.
- `noDurableFacts`로 명시 완료된 청크는 파생 레코드가 없어도 정상 완료로 유지한다.

### 1.3.11 기존 채팅 복구

- 분석 스키마 버전이 없는 과거 완료 청크는 기존 파생 상태를 삭제하지 않고 1.3.12 분석 계약으로 다시 대기한다.
- 과거 permanent failure와 in-flight 표시는 분석 버전 전환 시 한 번 초기화하여 새 parser/prompt로 재시도할 수 있게 했다.
- 채팅 수정, 청크 hash 변경, 므네메 정원 재정렬 시 분석 manifest와 청크 분석 메타데이터도 함께 무효화한다.

### 컨텍스트와 출력 안정성

- batch마다 앞선 병합 결과에서 만든 압축 `continuityBaseline`을 전달한다.
- 같은 batch의 청크는 시간순으로 분석하도록 명시했다.
- 자동 설정(`coldStartMaxChunksPerRun: 0`)은 한 진행에서 최대 4개 청크만 처리하여 오래된 대형 채팅의 무제한 호출을 막는다.
- 분석 manifest가 추가된 출력 크기를 반영해 batch 출력량 예측을 상향했다.
- 누락·절단·계약 위반 응답은 실패로 확정하기 전에 해당 청크만 같은 턴에 한 번 JSON 계약 복구 호출을 수행한다.

### 진단 UI

- 자동 기억 엔진에서 분석 완료, 상태 병합, 영속 변화 없음, 분석 대기 수를 분리해 표시한다.
- 최근 청크의 분석/병합 상태, 범주 판정, 파생 링크 수, 무결성 재대기 수를 확인할 수 있다.

### 분석 manifest 저장 증폭 차단

- `coldStart.analyses`, `processedHashes`, `failed`, `inFlight`를 한 blob에 통째로 다시 쓰던 구조를 안정 청크 참조로 분리했다.
- 분석 4개가 추가될 때 완료된 기존 분석 청크는 그대로 재사용하고 현재 tail 청크만 새로 쓴다.
- 현재 상태·백업·스냅샷의 mark-and-sweep GC가 중첩 분석 청크 참조까지 추적한다.
- 이전 버전 reader는 새 중첩 청크를 이해하지 못해도 rollback projection과 기존 checksum으로 나머지 상태를 복원하고, 분석 manifest만 안전하게 재대기한다.
- 204개 분석과 16개 스냅샷 fixture에서 도달 가능한 state blob만 남았고, 전체 manifest 세대 복제 가정치 `3,948,760`자 대비 실제 보존 blob 원문은 `324,715`자로 제한됐다.

### 삭제 채팅 저장소 자동 회수

- 구현과 단위 테스트만 존재하고 실제 요청 경로에서 호출되지 않던 `reconcileDeletedScopeStorage()`를 요청 경계에 연결했다.
- 캐릭터의 전체 채팅 목록과 모든 채팅의 영구 `erosTowerChatId`/host chat ID가 확인된 경우에만 삭제된 채팅 scope를 회수한다.
- fallback ID가 하나라도 있으면 채팅 순서 변경을 삭제로 오판할 수 있으므로 파괴적 GC를 건너뛴다.
- stale scope 여러 개를 한 번에 제거하고 canonical GC도 한 번만 수행한다.
- 해당 scope의 상태 커밋·canonical 주석·통신 저장 작업이 진행 중이면 삭제를 미루고 작업 해제 후 다시 시도한다.
- 채팅 목록뿐 아니라 scope index 지문도 비교하므로 같은 목록 아래 나중에 생긴 stale scope도 다음 요청에서 정리한다.
- canonical GC가 일시적 읽기 실패로 중단되면 완료로 캐시하지 않고 다음 요청에서 다시 시도한다.

## 수정 파일

- `ErosTower.v1.update.js` — 정식 1.3.12 업데이트 채널
- `☸에로스 타워.js` — 정식 채널과 byte-identical한 로컬 설치본
- `ErosTower.update.js` — 호환 업데이트 채널 4.0.49, 본문 동일
- `.test-storage-1.3.12.json` — 전체 회귀 결과

## 작업 전 백업

- `C:\Users\admin\Documents\에로스타워\백업\ErosTower_v1.3.11_before_cold_start_analysis_integrity_20260722-221857`
- `C:\Users\admin\Documents\에로스타워\백업\ErosTower_v1.3.12_before_storage_growth_fix_20260722-231900`
- 백업 폴더의 `SHA256SUMS.txt`에 원본 파일 해시와 본문 동기화 상태를 기록했다.

## 검증

- `node --max-old-space-size=128 --check` — 배포 파일 3종 통과
- ESLint 8.57.1 `no-redeclare`, `no-dupe-keys` — 통과
- 전체 내장 테스트 — 203개 테스트, boolean 1,707개, true 1,684개, 기존 진단 false 23개, 실행 오류 0개
- 1.3.12 신규 cold-start 분석 무결성 검증 12개 — 모두 통과
  - 모든 분석 범주 필수
  - facts 분석의 빈 commit 거부
  - 선행 무관 JSON을 건너뛰는 batch parser
  - facts/no-facts 완료 상태 분리
  - 관계·호감도 상태 병합
  - versioned chronological baseline 전달
  - 잘린 batch의 같은 턴 1회 계약 복구
  - 파생 관계 소실 시 해당 청크만 재대기
  - 기존 파생 상태를 보존한 1.3.11 재분석
  - 구 permanent failure 초기화
  - 한 진행 4청크 상한
- 신규 저장소 수명 검증:
  - 분석 manifest 중첩 청크 왕복과 완료 청크 재사용
  - 처리 hash 독립 청크
  - 스냅샷 16개 상한과 도달 가능한 blob만 보존
  - 이전 reader rollback projection
  - 영구 ID가 있는 삭제 채팅 scope만 자동 회수
  - fallback ID 포함 목록의 파괴적 GC 차단
  - 동일 목록 1회 처리, 새 stale index 감지, 중단된 canonical GC 재시도
  - 진행 중인 scope 저장 작업의 삭제 보류와 해제 후 회수
- 정식 채널과 로컬 설치본 byte-identical 확인
- 호환 채널은 상단 5줄 metadata를 제외한 본문 diff 0 확인

## 최종 SHA-256

- `ErosTower.v1.update.js`: `ED072521A385E3CCAAEEF2ABA9976273B44659FDABD8825F9829492C7C17E729`
- `☸에로스 타워.js`: `ED072521A385E3CCAAEEF2ABA9976273B44659FDABD8825F9829492C7C17E729`
- `ErosTower.update.js`: `133BD86C4E8C959C90F8DC25D15F17B6CF404820EBAC1FF662D917D1C34920BF`

로컬 배포 파일 준비까지만 완료했으며 원격 저장소 push는 수행하지 않았다.
