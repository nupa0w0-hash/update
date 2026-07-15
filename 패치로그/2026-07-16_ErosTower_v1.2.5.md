# Eros Tower 1.2.5

## 배포 범위

- 일반 업데이트 채널: `ErosTower.v1.update.js` / `1.2.5`
- 로컬 설치 파일: `☸에로스 타워.js` / `1.2.5`
- 기존 4.x 업데이트 호환 채널: `ErosTower.update.js` / `4.0.33`
- 작업 범위는 에로스 타워 플러그인과 릴리스 파일뿐이다. PocketRisu와 RisuAI 본체는 변경하지 않았다.

## 변경

### 설정 백업과 플러그인 저장소

- 설정 백업 가져오기는 현재 저장 설정 위에 가져온 설정을 병합하고, 마스킹된 키나 손상된 JSON을 저장하지 않는다.
- 저장소 정리는 에로스 타워 namespace만 대상으로 하며 마지막 저장 설정과 그 설정이 참조하는 이미지 프리셋 자료는 보존한다.
- 정리 도중 실패하면 삭제 전 snapshot으로 되돌리고 키 목록과 원문을 다시 검증한다.
- 설정 저장·가져오기·대시보드 작업·요청 처리와 저장소 정리가 동시에 실행되지 않도록 생명주기를 분리했다.

### 대화, 로어, 인물 권위와 주입 예산

- 실제 저장 대화와 선택된 오프닝을 우선해 첫 메시지와 현재 스토리가 분리되지 않도록 했다.
- 로어 재귀 활성화와 현재 등장 근거를 분리하고, 상태창·자산 목록 같은 자료를 인물 identity 원문으로 잘못 고정하지 않는다.
- 인물 별칭, 성별, 나이, 안정 외형은 canonical identity 근거에서 해석하고 현재 장면의 복장·장비·부상 같은 가변 상태와 분리한다.
- main injection 예산은 모델 context, 응답 예약량, 현재 메시지 사용량을 함께 계산하며 성공한 pre-agent 노트를 제한된 briefing으로 전달한다.

### Pre-agent와 OpenAI 호환 응답

- 일반 빈 응답은 응답 메타데이터를 보존한 채 한 번 재시도한다.
- 숨은 reasoning이 출력 예산을 모두 사용한 경우를 일반 빈 응답과 구분해 `reasoning-only output budget exhausted`로 진단한다.
- OpenAI 호환 응답의 공개 본문, tool call, reasoning 메타데이터를 분리하고 이미지 플래너의 구조화 JSON 복구 범위를 이미지 요청으로 제한한다.
- OpenAI 공식 및 GPT-5/o-series 계열에는 `max_completion_tokens`를 사용하고, GLM/Z.ai 호환 경로에는 지원되는 경우 thinking 비활성화 요청을 보낸다.

### 번역, 에셋, 상태창과 채팅 이미지

- 원문 병행 번역에서 에셋, 상태창, 생성 이미지가 원문과 번역본 양쪽에 중복 출력되지 않도록 구조 단위 표시 정책을 적용한다.
- 생성 이미지는 RisuAI 기본 `{{image::...}}` 표시를 사용하며 로컬 크기·비율 frame 설정을 제거했다.
- 앨범에는 저장됐지만 실제 assistant 메시지에서 빠진 이미지 artifact를 추적하고 저장 대화와 다시 동기화한다.

### 이미지 플래너 v18와 인물 유지

- 선택한 `[S#]` 장면만 visible cast를 결정하고 identity anchor는 이미 등장한 인물의 신원만 해석한다. 페르소나·화자·소유자는 자동 등장시키지 않는다.
- 같은 턴의 동일 인물은 안정 외형 블록을 유지하되 복장, 장비, 소품, 부상, 소비·분실·교체는 최종 출력의 최신 장면 상태로 갱신한다.
- NAI V4의 공통 장면 caption과 인물별 character caption을 분리하고 native asset의 이름·별칭·identity를 canonical 인물과 연결한다.
- 앨범 제목과 글귀는 선택 장면의 구체적 순간과 감정 흔적을 담은 짧은 한국어 문구로 제한하며 문단 통삽입과 `장면 S#` fallback을 사용하지 않는다.
- NovelAI 전용 프리셋 6개와 챈섭/Comfy용 `웹노벨` 프리셋을 형식별로 분리하고, 저장된 비호환 조합은 실제 호출 형식에 맞는 기본 프리셋으로 해석한다.
- 설정 저장 경로가 존재하지 않는 `imageApiFormatFromUI`를 참조하던 스코프 오류를 제거하고 현재 이미지 에이전트의 형식을 직접 읽는다.

## 프로바이더 경계

- 프로바이더가 hidden reasoning에 출력 예산을 전부 사용하고 `finish_reason: length`만 반환한 경우 플러그인은 원인을 정확히 분류하지만 동일 요청을 자동 반복하지 않는다.
- 프록시가 thinking 비활성화 파라미터를 무시하는 모델에서는 출력 예산 또는 모델의 reasoning 설정을 프로바이더 측에서 조정해야 한다.

## 로컬 검증

- 세 배포 파일 `node --check`: 통과
- 세 배포 파일 ESLint `no-undef`: 통과 (`Buffer`는 지원 runtime global로 선언)
- `git diff --check`: 통과
- 내장 zero-argument 회귀검사: `119/119` 실행, 예외 0
- 이번 변경 집중 의미검사: `68/68` 통과
  - 이미지 프리셋 형식 분리: `10/10`
  - 번역 구조 표시 정책: `15/15`
  - 이미지 인물·에셋 grounding: `27/27`
  - 설정 병합, 저장소 namespace 격리, 손상 시 rollback, main injection budget, 저장 assistant 이미지, 같은 턴 가변 상태, v18 prompt 이관·계약: 통과
- `ErosTower.v1.update.js`와 `☸에로스 타워.js`: 전체 내용 동일
- `ErosTower.update.js`: 호환 metadata를 제외한 본문 동일
- literal API key·token·개인정보 및 PocketRisu 변경: 없음

### 배포 후보 SHA-256

- `ErosTower.v1.update.js`: `8063F68F3F1B1EA9F44208A29C3D01505238E3E31103D0222DBCE5F560A92C0F`
- `☸에로스 타워.js`: `8063F68F3F1B1EA9F44208A29C3D01505238E3E31103D0222DBCE5F560A92C0F`
- `ErosTower.update.js`: `22141B5E47B0E2468C2796FEC8187DFAB9536E38905705CA8292B9C9FCEACFC5`

## 되돌림

- 직전 원격 기준: `f62f1097be6d06a459053bd1b650cde1d92a34f2` (`1.2.4`)
- 릴리스 승격 직전 후보 백업: `../../백업/ErosTower_before_v1.2.5_release_20260716-041458/`
- 배포 후 문제가 확인되면 이번 릴리스 커밋을 되돌리고 세 raw 업데이트 채널을 위 1.2.4 기준으로 복구한다.

## 원격 배포 검증

- 배포 후 커밋 고정 raw URL과 실제 `main` 업데이트 URL의 버전, SHA-256, cache header를 로컬 파일과 대조한다.
