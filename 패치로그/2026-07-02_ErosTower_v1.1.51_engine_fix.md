# Eros Tower 1.1.51 Engine Fix

작성 시각: 2026-07-02 11:18 KST

## 진단 근거

- 진단 패키지: `tmp_drive/drive_1PprXPPIWoS4UQx9w74X6Ds5pnpybc7Kk.fresh.bin`
- 확인된 반복 증상:
  - `commitReason: fallback:json-parse-failed`가 3회 반복됨.
  - 상태 커밋 실패 시 `characters.unknown` 중심으로 상태가 약하게 남음.
  - `knowledgeBoundary=true` 원천 로어가 active canonical trace에 들어갈 수 있었음.
  - pre-agent notes가 `Response Mandate`, 숫자 분량, 장면 강제 문구를 메인 모델에 너무 강하게 전달할 수 있었음.
  - 진단 패키지에서 실제 커밋 실패 상세 원인을 바로 확인하기 어려웠음.

## 백업

- 전체 엔진 수정 전:
  - `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_engine_fix_20260702-110337`
- state commit 수정 전 중간 백업:
  - `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_mid_before_state_commit_fix_20260702-110526`
- 패키지 본파일 동기화 전:
  - `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_package_sync_20260702-111015`

## 변경 내용

1. Knowledge-boundary canonical gate
   - `knowledgeBoundary` 원천 canonical unit은 최신 사용자 입력이 비밀/공개/스포/숨김 등 경계 의도를 명시할 때만 메인 주입 후보가 되도록 변경.
   - 진단 trace의 boundary preview는 원문 노출 대신 redacted 문구로 표시.
   - active lore bridge와 main control floor 양쪽 모두 동일한 boundary gate를 적용.

2. Deterministic state commit baseline
   - LLM state commit이 JSON 파싱 실패를 내도 deterministic baseline commit을 먼저 만들고 적용.
   - scene, present cast, canonical identity character, lore ledger, memory ledger, event log를 최소 상태로 남김.
   - `characters.unknown`으로 빠지는 대신 canonical identity 기반 이름을 우선 사용.

3. Pre-agent notes 권한 강등
   - 메인 주입용 pre-agent notes는 advisory note로만 전달.
   - `Response Mandate`, `Response Shape`, `Breadth`, 숫자 분량 지시, forced transition 계열 문구는 메인 주입 전 제거.
   - 에이전트 노트의 관찰/리스크/가능성은 남기되, 메인 답변 형식과 분량을 강제로 끌고 가지 않게 조정.

4. 진단 패키지 보강
   - run log compact JSON에 `failedCommitReason` 추가.
   - 진단 이벤트에 `commit-failed-detail` 추가.
   - 커밋 실패 원인을 사용자가 내보낸 진단 파일에서 바로 확인 가능하게 변경.

5. 패키지 동기화
   - `ErosTower.v1.update.js`와 `☸에로스 타워.js`를 동일 내용으로 동기화.

## 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- `git diff --check`: 통과.
- 기능 테스트:
  - `*says nothing*` 첫 진행 시 visible canonical lore `연수`, `청허문` 주입 확인.
  - `knowledgeBoundary` 비밀 원문은 메인 주입에서 제외 확인.
  - LLM 커밋 응답을 의도적으로 `this is not json`으로 깨뜨린 상태에서 `fallback:json-parse-failed` 기록 확인.
  - fallback 상태에서도 `characters: 연수`, memory ledger, lore ledger, event log 생성 확인.
  - `failedCommitReason: json-parse-failed` run log 기록 확인.

## 배포 상태

- 로컬 파일만 수정 완료.
- 사용자의 명시 요청 전까지 GitHub 배포/푸시는 하지 않음.

---

## 추가 진단 반영: 1hAqBHxWLCGTvFDYIhOs7QvxNliT-QxvI

작성 시각: 2026-07-02 11:45 KST

### 진단 근거

- 진단 패키지: `tmp_drive/drive_1hAqBHxWLCGTvFDYIhOs7QvxNliT-QxvI.bin`
- 확인 내용:
  - visible canonical lore는 메인 주입에 들어감: `연수`, `청허문`, `흑련교`, `조정`.
  - `knowledgeBoundary=true` 원천 비밀은 canonical trace에 직접 노출되지 않음.
  - state commit 실패는 계속 발생: `fallback:json-parse-failed`.
  - 이전 패치의 deterministic fallback은 작동: characters 1, memories 1, lore 4, events 1.
  - `Response Mandate`, `Response Shape`, `Recommended Entrances` 계열 agent note가 메인 진행을 과하게 유도할 수 있었음.
  - 같은 최종 출력 요약이 memory/event로 중복 주입되는 trace가 확인됨.
  - 실패 원인 분석에 필요한 commit prompt가 비어 있었음.

### 추가 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_diag_1hAq_fix_20260702-113727`

### 추가 변경

1. Advisory note sanitizer 보강
   - 메인 주입용 pre-agent notes에서 지시 헤딩 한 줄만 제거하던 문제를 수정.
   - `Response Mandate`, `Response Shape`, `Recommended Entrances`, `Focal and Continuity Plan`, `Primary Causal Movement` 등은 섹션 단위로 제거.
   - `Boundary`, `Breadth`, `Length`, `Action this response`, `Changed state`, `Earned decision` 같은 진행 강제 라인도 제거.
   - 관찰/앵커 성격의 `Current Theater`류 정보는 유지.

2. Retrieval 중복 제거
   - 같은 본문 요약이 memory/event/continuityRisk로 반복 선택되는 경우 하나만 남기도록 선택 단계에서 dedupe 추가.
   - 메인 주입과 state/agent retrieval 양쪽의 불필요한 반복을 줄임.

3. 실패 진단 trace 보강
   - JSON 파싱이 필요한 source ingest, cold start, state commit 경로는 실패 분석용 prompt trace를 짧게 생성.
   - 일반 성공 로그는 무겁게 만들지 않고, 실패/경고 항목에만 짧은 prompt/raw를 유지.

### 추가 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- `git diff --check`: 통과.
- 기능 테스트:
  - boundary 비밀 미주입 확인.
  - `fallback:json-parse-failed` 상태에서도 `연수` 캐릭터와 memory/lore/event fallback commit 확인.
  - 실패 run log에 `commitRawPreview`와 `commitPromptPreview`가 남는 것 확인.
  - 가짜 pre-agent가 `Response Mandate`/`Recommended Entrances`를 반환해도 메인 주입문에는 제거되고 `SAFE_ANCHOR_FACT` 같은 관찰 정보만 남는 것 확인.

---

## 추가 진단 반영: 1GWtxzLWcWT_740Hp0qXDmHdgs-7ssF4u / 1u-hDyb1BQkP4BXkR7GT_Jetxk0CrCr7-

작성 시각: 2026-07-02 12:08 KST

### 진단 근거

- 진단 패키지:
  - `tmp_drive/drive_1GWtxzLWcWT_740Hp0qXDmHdgs-7ssF4u.bin`
  - `tmp_drive/drive_1u-hDyb1BQkP4BXkR7GT_Jetxk0CrCr7-.bin`
- 확인 내용:
  - canonical lore 자체는 메인 주입 trace에 존재함: `First Message`, `Character Description`, `연수`, `조정`, `흑련교`, `번역가의 노트 1`.
  - 위 주입 canonical은 `knowledgeBoundary=false`였고, 이번 두 진단에서는 boundary 비밀 원문이 canonical trace에 직접 노출된 증상은 확인되지 않음.
  - `state-commit` agent의 저장 설정 `maxTokens`가 128로 들어가 있었고, 실제 raw 응답이 fenced JSON 중간에서 잘려 `json-parse-failed`가 발생함.
  - deterministic fallback은 작동했지만, 1인칭 소설 출력에서 주인공 이름이 본문에 직접 나오지 않으면 present cast/character commit이 약해질 수 있었음.
  - raw final output에는 `<Thoughts>` 블록이 여전히 발생했음. 최종 출력 정리 단계에서는 제거되지만, 모델이 사고/초안 블록을 생성하는 경향 자체는 별도 관찰 대상으로 남김.
  - agent note가 미래 타임라인과 후반 사건을 많이 담는 경향은 확인됨. 직전 sanitizer 보강으로 메인 주입 시 강제 지시 섹션은 제거되지만, 이후 테스트에서 영향도를 계속 봐야 함.

### 추가 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_psyche_token_floor_20260702-120055`

### 추가 변경

1. Psyche 계열 agent 출력 토큰 하한 보정
   - `state-commit` / `psyche-main`은 최소 2048 토큰을 사용하도록 실제 호출 단계에서 하한을 적용.
   - `state-aux` / `psyche-aux`는 최소 1536 토큰을 사용하도록 보정.
   - UI나 저장 설정에 128 같은 값이 남아 있어도 JSON commit이 구조적으로 중간 절단되지 않게 함.
   - 일반 agent의 최소값은 기존 128을 유지하여 전체 토큰 사용량을 무작정 키우지 않음.

2. 1인칭 출력 fallback present cast 보강
   - 최종 출력이 `나/내가/나는/I/我` 중심이고 canonical subject가 존재하면, 이름이 직접 언급되지 않아도 첫 canonical subject를 present cast 후보로 사용.
   - `state.activePerspective.presentCast`, `state.scene.presentCast`, `context.state.scene.presentCast`를 먼저 참고하고, 없을 때만 1인칭 canonical subject fallback을 적용.
   - `*says nothing*`류 소설 진행에서 주인공 이름이 본문에 반복되지 않아도 character commit이 빠지지 않게 보강.

### 추가 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- `git diff --check`: 통과.
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `2C237BF11BC004E0E71A6E82E4BD3FC28EFFA9C302BE8188D5DE493AD3C92036`
- 기능 테스트:
  - 저장 설정상 `state-commit maxTokens=128`인 조건에서도 실제 API 요청 `max_tokens=2048` 확인.
  - 1인칭 한국어 최종 출력에서 주인공 이름이 직접 나오지 않아도 `characters: 연수` fallback commit 확인.
  - scene, character, memory, lore, event fallback commit 생성 확인.

---

## 추가 수정: maxTokens 사용자 상한 제거

작성 시각: 2026-07-02 12:13 KST

### 수정 이유

- 기존 코드에는 agent/global `maxTokens` 정규화와 설정 UI에 `16000` 상한이 남아 있었음.
- Vertex Gemini 등 최신 provider에서 사용자가 `65536` 같은 큰 출력값을 의도적으로 쓰는 경우, 플러그인 내부에서 먼저 잘라버리는 것은 부적절함.
- provider별 실제 허용 한도는 provider/API가 판단해야 하므로, 에로스 타워의 인공 상한을 제거함.

### 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_max_token_cap_remove_20260702-121309`

### 변경 내용

1. `maxTokens` 상한 제거
   - 전역 `merged.maxTokens` 정규화에서 `16000` 상한 제거.
   - provider preset `maxTokens` 정규화에서 `16000` 상한 제거.
   - agent 저장 처리 `et-agent-max-tokens` 정규화에서 `16000` 상한 제거.
   - 설정 UI의 `Max Tokens` input에서 `max="16000"` 제거.

2. Psyche 계열 안전 하한 조정
   - `state-commit` / `psyche-main` 최소 출력값을 4096으로 조정.
   - `state-aux` / `psyche-aux` 최소 출력값을 4096으로 조정.
   - 이는 JSON commit 중간 절단 방지용 하한이며, 사용자가 더 큰 값을 넣으면 그대로 보존됨.

### 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- `git diff --check`: 통과.
- `maxTokens` 관련 `16000` 상한 잔존 여부 검색: 없음.
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `D57BEAFD2C10FBAABCC6869F0FFE9B3CCAFD72FEA73D83382FEDED9206642C16`

---

## 추가 수정: 세션 발생 세계 인물 흡수

작성 시각: 2026-07-02 12:58 KST

### 진단 근거

- 진단 패키지:
  - `tmp_drive/drive_1cAMZ7nL5dDwaShUCab14O8LgNTJNsl5_.bin`
- 확인 내용:
  - `state-commit` / `state-aux`는 4096으로 올라가 있었고 `json-parse-failed`는 발생하지 않음.
  - canonical lore 주입도 정상 확인.
  - 최종 출력에는 `명선(明仙) 사형`이 실제 등장했지만, 해당 턴 commit은 `characters: 1`, `relationships: 0`으로 새 세계 인물을 흡수하지 못함.
  - 일부 기본 에로스 에이전트 노트가 `unnamed senior`, `Do not invent...` 식으로 읽혀 새 인물/백스테이지를 좁히는 방향으로 작동할 수 있었음.

### 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_session_world_person_patch_20260702-125424`

### 변경 내용

1. 기본 에로스 에이전트 프롬프트 수정
   - `새 인물 금지`처럼 읽히는 문구를 제거/완화.
   - 세션에서 자연 발생한 인물, 지역 주민, 사형/동문, 하인, 문지기, 장사꾼, 소문 출처, 오프스크린 인물을 살아있는 세계 구성원으로 다루도록 변경.
   - 원천 canonical 인물과 세션 발생 인물은 구분하되, 세션 발생 인물을 disposable extra로 버리지 않도록 명시.
   - prompt revision을 `v1.1.51-session-world-persons`로 올려 기본 프롬프트 갱신이 적용되게 함.

2. State Committer 지시 보강
   - 최종 출력에서 새로 이름 붙은 인물이 생기면 `session-born`, `body-memory`, `rumor`, `offscreen` 등의 origin으로 commit하도록 지시.
   - `firstSeenTurn`, `affiliation`, `role/title`, `aliases`, 관계, evidence quote를 남기도록 schema와 지시를 확장.

3. deterministic baseline 보강
   - 최종 출력에서 `이름 + 역할/호칭` 패턴을 감지해 세션 발생 세계 인물로 추출.
   - 한국어 조사 형태(`명선 사형이`, `명선 사형은`)를 인식.
   - 추출된 인물을 `scene.presentCast`, `characters`, `relationships`, `memoryLedger`에 자동 반영.
   - 예: `명선(明仙) 사형` → `명선`, alias `明仙`, role `청허문 사형`, origin `body-memory`, relation `연수:명선`, memory `turn-2-person-명선`.

4. debug 테스트 훅 추가
   - `__EROS_TOWER_DEBUG.testSessionWorldPersonCommit()` 추가.
   - 진단 재현 문장으로 세션 발생 인물 흡수가 되는지 빠르게 확인 가능.

### 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- `git diff --check`: 통과.
- 내부 실행 검증:
  - 입력: `청허문의 마당 저편에서 명선(明仙) 사형이 다가왔다...`
  - 결과:
    - `presentCast`: `연수`, `명선`
    - `characters`: `명선`, `연수`
    - `relationships`: `연수:명선`, tie `청허문 사형`
    - `memoryLedger`: `turn-2-final-output`, `turn-2-person-명선`
    - `commitCounts`: characters 2 / relationships 1 / memories 2
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `65A5E0EAFAB73438D103B605F13CFEB94E9E15B1055CCEE488457CEDBCF26C08`

---

## 추가 진단 반영: 11IZ0H9Y7bcXhNhDCq4m-TpJU19vO7A_y

작성 시각: 2026-07-02 13:30 KST

### 진단 근거

- 진단 패키지:
  - `tmp_drive/drive_11IZ0H9Y7bcXhNhDCq4m-TpJU19vO7A_y.bin`
- 확인 내용:
  - `state-commit` / `state-aux`는 4096으로 적용됨.
  - `json-parse-failed` 없음.
  - canonical lore 주입 정상.
  - 세션 발생 인물 패치가 작동: `정명 (청허문 사형)`이 `turn-2-person-정명` memory로 들어감.
  - commit 결과도 `characters: 4`, `relationships: 2`, `memories: 5`로 상승.
  - 다만 이전 상태에서 생긴 `characters.unknown`이 `渊水 | active` 형태로 retrieval trace에 남아 중복 주입 후보가 됨.

### 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_unknown_character_prune_20260702-132735`

### 변경 내용

1. `characters.unknown` 자동 정리
   - `id`가 `unknown`, `characters.unknown`, `character:unknown`인 캐릭터 상태를 prune 대상으로 추가.
   - 세션 발생 인물 보존과는 별개로, 불량 id 캐릭터가 retrieval에 계속 올라오는 것을 방지.
   - 다음 실행 시 `syncCurrentCharacterBootstrap` 단계에서 기존 `characters.unknown`이 정리됨.

### 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- 내부 실행 검증:
  - `state.characters.unknown = { id: "unknown", name: "渊水" }`를 넣고 bootstrap 실행.
  - 결과: `unknown` 제거, 정상 세션 인물 `정명` 유지, character-card 주체 `연수` bootstrap 유지.
- `git diff --check`: 통과.
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `CC4A53CEF6117B2DD87EEFEDDFD3779CE08ADD6B547232A3CBA730175E4CDFD6`

---

## 추가 수정: 무페르소나 placeholder 무효화

작성 시각: 2026-07-02 13:36 KST

### 수정 이유

- 풀사칭/소설 감상형 사용자는 일부러 빈 페르소나, `없음`, `관찰자`, `Observer`, `User` 같은 표시값을 사용함.
- 기존 코드는 선택 페르소나에 이름만 있으면 `activePerspective.presentCast`, `[Persona]`, `{{user}}/{{persona}}` 치환, 번역 슬롯, 품질 보호어에 들어갈 수 있었음.
- 이 경우 실제 인물이 아닌 placeholder가 관점/등장인물 후보처럼 작동할 수 있으므로, 내용 없는 무페르소나 표시값은 에로스 타워 내부에서도 없는 것으로 처리함.

### 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_empty_observer_persona_patch_20260702-133436`

### 변경 내용

1. 유효 페르소나 판정 추가
   - `getEffectiveSelectedPersona`
   - `formatEffectivePersonaBlock`
   - `cleanPersonaSettingText`
   - `isNoPersonaPlaceholder`
   - `isNoPersonaMarkerText`

2. 무페르소나 표시값 처리
   - 내용 없는 `없음`, `없어`, `없다`, `무페르소나`, `빈 페르소나`, `관찰자`, `관전자`, `구경꾼`, `독자`, `시청자`, `Observer`, `Spectator`, `Viewer`, `Reader`, `User`, `Anonymous` 등을 주입에서 제거.
   - 단, 같은 이름이라도 `description/desc/prompt/note/content`에 실제 설명이 있으면 유효 페르소나로 유지.
   - 이름만 있는 실제 페르소나 예: `김철수`는 기존처럼 유지.

3. 적용 지점
   - 현재 관점 bootstrap의 `presentCast` 후보.
   - `[Persona]` setting block.
   - canonical lore의 `{{user}}` / `{{persona}}` 변수 치환.
   - 번역 에이전트 `{{slot::persona}}`.
   - 품질관리 보호어 추출.
   - 세션 fingerprint의 persona id/hash.

### 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- `node --check .\☸에로스 타워.js`: 통과.
- helper 추출 테스트 통과:
  - 빈 object: 무페르소나 처리.
  - `관찰자` 이름만 있음: 무페르소나 처리.
  - `없음` 이름만 있음: 무페르소나 처리.
  - `관찰자` + 실제 설명 있음: 유효 페르소나 유지.
  - `김철수` 이름만 있음: 유효 페르소나 유지.
  - `Observer` / `User` 이름만 있음: 무페르소나 처리.
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `9254A640C0FC2C49C92F9F80273F541B5D2AE0B3B234077EA7C06B041C886BFF`

---

## 추가 진단 반영: 13swJLEkuwPNq07SnPtRCGz8u1DWDGB3j

작성 시각: 2026-07-02 14:07 KST

### 진단 근거

- 진단 패키지:
  - `tmp_drive/drive_13swJLEkuwPNq07SnPtRCGz8u1DWDGB3j.bin`
- 확인 내용:
  - `New Chat 3`, 메시지 4개, mode `novel`, canonical sources 13 / units 20.
  - 길이 토글은 `0 / 자연`, malformed residue 없음.
  - `state-commit` JSON 실패 없음.
  - 문제는 로어를 못 본 것이 아니라, 첫 턴부터 `남궁묵야`, `흑련교`, `천마`, `929`, `원작/미래 루트` 계열이 main/agent retrieval에 넓게 들어간 것.
  - `boundaryReason: future/original-route-marker`는 찍히지만 `knowledgeBoundary`에는 반영되지 않아 실제 차단/권한 조절에 쓰이지 않았음.
  - `activeLoreBridgeQueryText`가 `[Character]` 전체 설명을 query로 사용해, 캐릭터 설명 안의 미래/원작 설정이 자기 자신을 활성화하는 구조가 있었음.
  - 세션 발생 인물 extractor가 `도포가 소년...`을 `도포가 (소년)` 인물로 오탐해 `turn-1-person-도포가` memory를 생성함.

### 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.51_before_future_route_and_session_person_patch_20260702-135350`

### 변경 내용

1. future/original-route unit 권한 분리
   - `futureSource` / `boundaryReason`을 `psycheSources`, `psycheChunks`, `psycheUnits`, canonical unit trace에 전달.
   - future/original-route 성격의 part는 `foundation` / `alwaysActive` 강제 주입에서 제외.
   - 사용자가 말없이 진행하는 경우라도 이미 실제 채팅 흐름에 원작/미래 축이 드러난 상태면 선택 가능.
   - 반대로 blank 진행에서 source-heavy 설명만으로 future-route가 자기활성화되는 것은 차단.

2. boundary-aware canonical splitter 추가
   - 미래/원작 marker가 있는 source는 일반 설정 part와 future-route part를 문단 경계 기준으로 분리.
   - 신원/현재 설정은 그대로 canonical/always로 보존하고, 미래 route 문단만 낮은 권한으로 분리.

3. retrieval query 정리
   - active lore bridge query에서 `[Character]`와 `[Canonical Lore Candidates]` 같은 source-heavy 섹션을 제거.
   - 현재 입력, 실제 최근 채팅, 에이전트 노트 중심으로 선택되게 조정.

4. state/psyche retrieval gate
   - future/original-route 파생 unit이 hot score만으로 main/agent에 올라오지 않도록 `canUseFutureOriginalCandidate` 추가.
   - 실제 채팅 흐름에 원작/미래 접근이 살아 있을 때는 유지.

5. 세션 발생 인물 오탐 방지
   - deterministic extractor에서 `소년/소녀/노인/사내/여인` 같은 일반 묘사 호칭은 자동 인물 추출 대상에서 제외.
   - `도포`, `소매`, `옷`, `문고리`, `손`, `공기`, `은사연검`, `황동경` 등 물건/감각 명사가 조사와 붙은 경우 인물명으로 보지 않음.
   - 이름+사회적 호칭 예: `명선(明仙) 사형`은 계속 세션 인물로 보존.

6. debug 검증 훅 추가
   - `__EROS_TOWER_DEBUG.testFutureRouteAndSessionPersonGate()`
   - Run Log에는 영향 없음.

### 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- VM stub으로 플러그인 로드 및 debug 테스트 호출:
  - blank `*says nothing*`: future-route canonical unit 선택 0개.
  - 최근 채팅에 `원작의 미래 루트와 929년 사건`이 드러난 상태: future-route unit 선택됨.
  - 일반 설정 part: `futureSource=false`, `alwaysActive=true`.
  - 미래 route part: `futureSource=true`, `alwaysActive=false`.
  - `도포가` false positive 없음.
  - `명선` 세션 인물 추출 유지.
- 패키지 SHA256 동기화 확인:
  - `ErosTower.v1.update.js`
  - `☸에로스 타워.js`
  - 두 파일 모두 `523D47594128333CE2BB2F992397B91764A62C102AA5BFC131388707C839BD82`
