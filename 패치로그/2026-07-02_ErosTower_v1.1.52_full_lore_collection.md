# Eros Tower 1.1.52 Full Lore Collection Fix

작성 시각: 2026-07-02 16:35 KST

## 상태

- 로컬 작업 완료.
- GitHub 배포/푸시하지 않음.

## 진단 근거

- 사용자 제보:
  - 사계 봇에서 로어북이 일부만 주입됨.
  - 진단 trace에는 일부 canonical source만 표시됨.
  - 원본자료 전체보기는 중간에서 잘린 것처럼 보임.
- 확인 파일:
  - `tmp_drive/drive_1wiT5meXLlDOmdPR4WfDLh2dDtvwSm7Z1.bin`
  - `tmp_drive/drive_14kSz2lM7ewEbZfhP1YR7NroNHFiYVyNs.bin`

## 확인한 원인

1. 캐릭터 로어북 수집 cap
   - `collectCanonicalSources()`가 캐릭터 로어를 `globalLore.slice(0, 48)`로 잘라 수집하고 있었음.
   - 사계 봇은 `character_book.entries`가 170개이고, 뒤쪽 핵심 로어가 index 156/167에 있었음.
   - 따라서 뒤쪽 핵심 로어는 canonical source 후보에 들어오지 못했음.

2. Chara Card v3 경로 누락
   - 카드 원본은 `data.character_book.entries` 구조.
   - 기존 수집기는 `characterBook` camelCase 위주라 `character_book` snake_case 경로가 약했음.

3. 원문 길이 절단
   - `normalizeCanonicalLoreContent()`가 source content를 12000자로 잘라 반환함.
   - 긴 인물 로어의 뒤쪽 원문이 수집 단계에서 유실될 수 있었음.

4. UI 전체보기 오표시
   - `원본 자료 전체보기`가 `compactJson()`을 사용하고 있었고, `compactJson()`은 260자로 자름.
   - 또한 source content도 500자 preview만 넣고 있어 전체보기가 실제 전체보기가 아니었음.

5. active bridge 선택 실패
   - 수집 cap 제거 후 VM 검증에서 `겨울왕국,왕자) 프레이르 스노우레이븐` source는 canonical source로 들어왔지만, `궁정에서 프레이르를 마주쳤다` 입력의 active-canonical에는 처음에 들어오지 않았음.
   - 원인:
     - active bridge query에 `[First Message]`, `[Author Note]`, 상태 템플릿 성격의 setting block이 섞여 `status`, `current`, `user`, `image` 같은 일반 토큰이 트리거처럼 작동함.
     - `packCanonicalUnits()`가 첫 후보는 예산 초과여도 넣는 구조라, 큰 foundation/always 항목이 active bridge 예산을 먼저 잡아먹음.
     - 직접 trigger가 잡힌 뒤에도 recursive 후보가 빈 예산에 무관한 짧은 조각을 채움.
     - 같은 원천의 먼 sibling 조각이 남은 예산에 들어가 첫 만남에 과한 내부/후반 정보가 섞일 수 있었음.

## 변경 내용

1. 수집 cap 제거
   - `globalLore.slice(0, 48)` 제거.
   - `localLore.slice(0, 48)` 제거.
   - module/reference lore의 48개 cap 제거.
   - 최종 `out.slice(0, 260)` 제거.
   - runtime lorebook entries의 260개 cap 제거.

2. Chara Card v3 lorebook 경로 추가
   - `character_book`
   - `data.character_book`
   - collect traversal 내부의 `character_book`

3. 원문 보존
   - `normalizeCanonicalLoreContent()`의 `text.slice(0, 12000)` 제거.
   - 수집 단계에서는 full canonical source text를 유지.
   - 주입량은 기존 canonical splitter / selector / budget packer가 처리.

4. 진단 UI 수정
   - `source audit` 추가:
     - total
     - byKind
     - alwaysActive
     - explicitCharacter
     - totalChars
     - maxSourceChars
   - `원본 자료 전체보기 (full canonical source)`는 source별 full content를 pretty JSON으로 표시.
   - `safeJsonStringify(value, space)`를 지원하도록 확장.

5. 검증 훅 추가
   - `__EROS_TOWER_DEBUG.testCharaCardLoreCollection(value)`
   - Chara Card JSON을 넣으면 raw lore entry, canonical source, Freyr/NPC list 수집 여부를 확인 가능.

6. active trigger selection 수정
   - 한국어 조사 토큰 보정 추가:
     - `프레이르를` -> `프레이르`
     - `궁정에서` -> `궁정`
   - active bridge query에서 setting block 원문을 제거하고 현재 사용자 입력/최근 대화/에이전트 노트 중심으로 축소.
   - direct activation score를 추가해 현재 입력의 key/label 직접 매칭이 foundation/always보다 밀리지 않도록 보정.
   - active bridge에서는 `firstMessage`를 제외.
   - active bridge에서는 첫 후보라도 예산을 초과하면 건너뛰도록 `allowOversizeFirst: false` 적용.
   - 직접 trigger가 있는 경우:
     - directScore가 있는 trigger/foundation 후보만 유지.
     - recursive 후보는 제외.
     - 같은 source base는 첫 진입 후보만 유지.
     - sibling completion은 인접 조각만 허용.

## 검증

- `node --check .\ErosTower.v1.update.js`: 통과.
- VM 함수 단위 검증:
  - raw lore entries: 170
  - canonical sources: 157
  - globalLore sources: 155
  - `겨울왕국,왕자) 프레이르 스노우레이븐`: 수집됨, content 19603 chars
  - `npc 리스트 (남캐버전)`: 수집됨, content 8954 chars
  - maxSourceChars: 19603
  - totalChars: 659332
- active bridge 재현 검증:
  - 입력: `궁정에서 프레이르를 마주쳤다`
  - query terms: `궁정에서`, `궁정`, `프레이르를`, `프레이르`, `마주쳤다`
  - active bridge canonical count: 1
  - 포함: `겨울왕국,왕자) 프레이르 스노우레이븐 p1/16`
  - 미포함 확인: `아티스`, `이그니스`
- main briefing 조립 검증:
  - `buildMainBriefing(..., 32000, ...)` 결과에 Freyr canonical 포함.
  - injection trace에 `active-canonical / 겨울왕국,왕자) 프레이르 스노우레이븐 p1/16` 포함.

## 수정 파일

- `ErosTower.v1.update.js`
- `☸에로스 타워.js`
- `ErosTower.update.js`
- `패치로그/2026-07-02_ErosTower_v1.1.52_full_lore_collection.md`

## 백업

- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.52_before_full_lore_collection_20260702-163549`


---

## 2026-07-02 Prompt Restore Addendum

Scope:
- Restored the built-in RP/Novel pre-agent prompt bodies from the supplied Eros pipeline preset exports.
- Restored only the 4 pre agents: world, character, momentum, synthesis.
- Did not restore the old post agent.
- Kept runtime `Source Handling` injection outside the stored prompt body.
- Removed unintended pre-agent prompt additions such as `session-born` / `world-born` wording by replacing the prompt bodies with the original preset text.

Length toggle handling:
- `toggle_길이` is detected as a host prompt signal for Eros agents.
- The injected note now says `Advisory for Eros Tower agents only`.
- Removed the old `Main prompt equivalent` wording.
- The length block tells agents to scale note breadth/pacing/front detail only; it does not instruct the final-response model to write a specific length.

Verification:
- `node --check .\update_repo\ErosTower.v1.update.js`: passed.
- VM comparison against `drive_1FCEZSrHdpVE47fiIltzq2ZXV4gfL_1m0.bin` RP preset: all 4 pre-agent `systemPrompt` and `outputInstruction` values matched exactly.
- VM comparison against `drive_1U4UFv63AnyNe5zMgq6Cfa30uznajMumB.bin` Novel preset: all 4 pre-agent `systemPrompt` and `outputInstruction` values matched exactly.
- Runtime pre-agent system prompt still appends `Source Handling`.
- `toggle_길이=4` produces an agent-advisory length block and no `Main prompt equivalent` text.

Backup folders:
- `ErosTower_v1.1.52_before_prompt_restore_20260702-183735`
- `ErosTower_v1.1.52_prompt_restore_sync_20260702-184250`


---

## 2026-07-02 Agent Note Runtime Addendum

Diagnostic source:
- `tmp_drive/drive_1WATSccPCw6vIgV6FatV2WLuKtNz_wdtd.bin`

Findings:
- Canonical lore visibility was present in the main trace: 157 canonical sources, 591 canonical units, 28.7K injected chars.
- Response length control was inactive: `toggle_길이=0/natural`.
- Despite inactive length control, pre-agent notes inferred unsupported long/epic scale. Examples: `epic scale`, `3200+ words`.
- Two pre-agent notes leaked planning preambles instead of compact structured notes:
  - character note: 19,757 chars, reasoning preamble before `[Focal Flow]`.
  - momentum note: 19,994 chars, reasoning preamble before `[Response Shape]`.
- Final synthesis then inherited the unsupported epic-scale assumption.

Fix:
- Main briefing no longer receives Eros Tower response-length control blocks.
- Pre-agent context receives response-length guidance only as agent advisory.
- When no host length toggle is active, pre-agents now get neutral guidance: use natural scale and do not infer long/chapter/epic scale from max context, max response tokens, source volume, first turn, or available budget.
- Added pre-agent note sanitization before notes are reused by later agents or the main bridge.
- Raw agent output remains in Run Log; sanitized `text` is used for pipeline reuse.
- Sanitizer cuts leaked reasoning preambles before first valid structured section, including bold section labels such as `**[Focal Flow]**`.

Verification:
- `node --check` passed for `ErosTower.v1.update.js`.
- VM check: main control floor does not include `[Response Length Control]` for inactive or active toggles.
- VM check: pre-agent advisory includes inactive neutral guidance when `toggle_길이` is absent/inactive.
- VM check: active `toggle_길이=4` still produces agent-only scale guidance and no `Main prompt equivalent` text.
- VM check on the diagnostic notes:
  - character note cleaned from 19,757 chars to 7,702 chars; reasoning preamble removed.
  - momentum note cleaned from 19,994 chars to 4,690 chars; reasoning preamble removed.

Backup:
- `D:\????\??? ??\??\ErosTower_v1.1.52_before_length_neutral_agent_note_cleanup_20260702-190400`


---

## 2026-07-02 Length Toggle Correction

Correction:
- User confirmed the test was run with `toggle_길이=5` / `???`.
- Therefore `Write at least 3200 words`, `epic scale`, and broader agent planning are expected for that run.
- The earlier diagnostic interpretation that treated `3200+` as an unsupported assumption was incorrect.

Remaining valid fix:
- Keep response-length handling agent-only.
- Prevent duplicate active length advisory in pre-agent context.
- Keep the inactive neutral advisory only for genuinely inactive or undetected length toggles.
- Keep pre-agent reasoning-preamble cleanup, because leaked `Let me think` / analysis text is still not useful as reusable agent notes.

Verification:
- VM check with `toggle_길이=5`: pre-agent advisory includes `Write at least 3200 words` exactly once.
- VM check with `toggle_길이=5`: main control floor has no response-length block.
