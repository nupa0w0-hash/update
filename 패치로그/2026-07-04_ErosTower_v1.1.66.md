# Eros Tower v1.1.66

- 날짜: 2026-07-04
- 상태: 로컬 패치. 배포/커밋/푸시 없음.
- 배포 예정 버전: `1.1.66`
- 호환 업데이트 채널 예정: `4.0.17`
- 백업 1: `D:\리수작업\에로스 타워\백업\ErosTower_before_local_1.1.66_session_deferred_nonblocking_20260704-000430`
- 백업 2: `D:\리수작업\에로스 타워\백업\ErosTower_before_local_1.1.66_nosession_nonblocking_20260704-002627`
- 백업 3: `D:\리수작업\에로스 타워\백업\ErosTower_before_local_1.1.66_transient_state_persist_20260704-005220`
- 백업 4: `D:\리수작업\에로스 타워\백업\ErosTower_before_local_1.1.66_empty_continuation_history_20260704-005836`
- 백업 5: `D:\리수작업\에로스 타워\백업\ErosTower_before_local_1.1.66_revert_session_confirmation_20260704-010755`

## 문제 증상

- RisuAI에서 `say nothing`이 아니라 완전 빈 입력으로 소설처럼 진행하면 user 입력 메시지가 남지 않을 수 있다.
- 빈 입력 연속 진행에서는 마지막 메시지가 assistant인 상태가 정상인데, 기존 `trimToLastUser` 기준은 마지막 user 이후 assistant 진행분을 context에서 잘라낼 수 있었다.
- 이 경우 세션 diff가 `3 -> 1`, `3 -> 2`처럼 보이고, 이전 로직은 이를 메시지 삭제/세션 읽기 의심으로 처리했다.
- 1.1.65에서는 번역/후처리는 복구했지만, 세션 guard 상태에서는 관리상태 커밋과 현재 출력 기반 기억 반영이 여전히 막힐 수 있었다.
- `currentChat` 자체가 일시적으로 비어 `context.noSession`으로 판정되면, 전처리 주입과 번역 후처리까지 통째로 return되는 경로가 남아 있었다.

## 원인

- 세션 보호가 “삭제/재정렬을 해도 되는가”와 “현재 출력으로 새 상태를 커밋해도 되는가”를 같은 guard로 묶고 있었다.
- 메시지 해시가 겹치지 않는 `3 -> 2` 형태는 실제 삭제라기보다 빈 입력/부분 읽기/메시지 재구성일 가능성이 큰데, 작은 채팅에서는 삭제 확정으로 승격될 수 있었다.
- `noSession`은 저장 scope가 불안정하다는 뜻이지, 캐릭터/DB/로어 원천을 전혀 못 읽었다는 뜻은 아니다. 이 상태에서 전체 엔진을 중단하면 위그로어식 “주입/선택과 파괴적 sync 분리” 원칙에 어긋난다.

## 핵심 변경

- 메시지 축소가 있어도 이전/현재 메시지 해시가 전혀 겹치지 않는 wholesale reshape는 삭제 확정이 아니라 `message-shrink-deferred`로 본다.
- `message-shrink-deferred`는 삭제/재정렬만 보류하고 전처리 에이전트, 로어 주입, 후처리 번역, 현재 출력 기반 관리상태 커밋은 계속 진행한다.
- 세션 읽기 불안정 중에는 오래된 채팅 백로그 chunk 재동기화만 건너뛰어 잘못 읽은 메시지 목록으로 장기기억을 지우거나 재구성하지 않는다.
- 실제 삭제가 확실한 경우에는 기존 `message-delete-confirmed` / `message-history-mutated` 경로로 므네메 정원 재정렬과 파생 기록 정리를 수행한다.
- `context.noSession`에서는 임시 상태로 canonical/NeuroCore/전처리 주입/번역 후처리/출력 정리를 계속 실행한다.
- `context.noSession`이어도 `charIndex/chatIndex`, `chatId`, 캐릭터 기반 fallback scope가 안정적이면 기존 state를 불러오고 관리상태 커밋/save/run log를 유지한다.
- `context.noSession`에서 보류하는 것은 삭제 rewind, 장기 백로그 재동기화, chat-history 기반 cold-start처럼 현재 host message 목록이 안정적이어야 하는 작업이다.
- scope가 `unknown-character:chat-unknown`처럼 완전히 불명확할 때만 state 저장과 state commit을 보류한다.
- `noSession` 실행은 저장 가능하면 `pre-complete-no-session-fallback-scope` / `complete-no-session-fallback-scope`, 저장 불가능하면 `pre-complete-no-session-transient` / `complete-no-session-transient`로 구분한다.
- 대시보드 최신 실행 경고에는 fallback 저장 가능 상태는 `세션 읽기 임시`, 저장 불가능 상태는 `세션 저장 보류`를 표시한다.
- `trimContextForActiveTurn`을 추가해 현재 요청/채팅이 user로 끝나는 일반 입력 턴에서는 기존처럼 마지막 user까지 자르고, user 입력이 없는 빈 진행 턴에서는 최신 assistant 출력까지 context에 유지한다.
- 사용자 확인 필수 UI/수동 승격 레이어는 회수했다. 므네메 정원은 기존처럼 자동으로 작동하며, 빈 입력/부분 읽기로 보이는 no-overlap shrink만 자동 보류한다.

## 수정 파일

- `ErosTower.v1.update.js`
- `☸에로스 타워.js`
- `ErosTower.update.js`
- `패치로그/2026-07-04_ErosTower_v1.1.66.md`

## 검증 예정

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- 본문 동기화 확인
- 세션 diff 재현 테스트: `3 -> 1`, `3 -> 2` no-overlap shrink는 삭제 확정이 아니라 deferred
- afterRequest 순서 확인: post pipeline 이후에도 session deferred 상태에서 state commit 경로가 남아 있음
- `noSession` 정적 경로 확인: beforeRequest/afterRequest에서 전체 return 없이 transient 실행, 저장/파괴적 동기화만 보류
- fallback scope 정적 경로 확인: `noSession`이어도 persistable scope면 state load/save와 state commit을 유지
- 빈 입력 연속 경로 확인: request/chat 모두 마지막 user가 아니면 assistant tail을 자르지 않는다
