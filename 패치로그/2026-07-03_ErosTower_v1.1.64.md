# Eros Tower v1.1.64

- 날짜: 2026-07-03
- 배포 버전: `1.1.64`
- 호환 업데이트 채널: `4.0.15`
- 백업: `D:\리수작업\에로스 타워\백업\ErosTower_before_v1.1.64_session_partial_read_release_20260703-233128`

## 문제 증상

- RisuAI/WebView가 현재 채팅 메시지를 일시적으로 1개만 읽는 경우, 세션 보호가 `3 -> 1` 같은 메시지 감소를 감지하고 `세션 보호 확인 필요` 경고가 누적되었다.
- 이전 로컬 교정 전에는 `firstChatId` 단독 변화가 세션 경계 리셋으로 이어져 관리상태 ledger가 0으로 보일 수 있었다.
- 메인 프롬프트에는 길이 지시가 정상 렌더링되어도 깨진 토글 잔여문 때문에 에로스가 길이 토글을 자연/비활성처럼 오판할 수 있었다.
- 구 4.x 설치본을 위한 compat 업데이트 채널도 함께 올려야 업데이트 표시가 정상 동작한다.

## 원인

- `firstChatId`는 Risu 메시지 표현과 진단 context에 따라 `et-registered-first-*` 또는 `0`처럼 바뀔 수 있는데, 이를 하드 세션 경계로 취급했다.
- partial chat read 형태가 반복되면 transient 보류를 지나 실제 삭제처럼 확정될 수 있는 조건이 남아 있었다.
- 대시보드 경고 요약이 `message-shrink-deferred` 같은 비파괴 보류 경고까지 `확인 필요`로 표시했다.
- 호스트 프롬프트에 남은 `5=6}}}}Write at least 5000 words` 같은 비활성 조건문 잔여문을 길이 감지 후보로 읽었다.

## 핵심 변경

- `scope`, `characterId`, `chatId` 변경만 세션 하드 리셋 조건으로 유지하고, `firstChatId` 단독 변경은 정규화 이벤트로 처리한다.
- 메시지 목록이 갑자기 1개 이하로 읽히는 partial-read 형태는 반복되어도 실제 삭제로 승격하지 않고 계속 보류한다.
- `message-shrink-deferred`는 진단 이벤트로 보존하되 대시보드의 actionable `세션 보호 확인 필요` 요약에서는 제외한다.
- 길이 토글 감지에서 깨진 조건문 잔여문을 제거하고 실제 활성 길이 지시만 판정한다.
- `MAIN_INJECTION_TITLE`, 표시명, 내부 `VERSION`, 패치노트를 `1.1.64`로 갱신한다.
- v1 채널, 직접 설치 파일, compat 채널을 함께 갱신한다.

## 수정 파일

- `ErosTower.v1.update.js`
- `☸에로스 타워.js`
- `ErosTower.update.js`
- `패치로그/2026-07-03_ErosTower_v1.1.64.md`

## 검증

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- 헤더 확인: v1 `//@version 1.1.64`, compat `//@version 4.0.15`
- 본문 동기화 확인: v1과 compat는 상단 5줄 제외 동일
- 길이 감지 재현 테스트 통과
- 세션 partial-read 재현 테스트 통과

## 배포 후 확인

- v1 raw URL 확인 완료: `//@display-name ☸Eros Tower 1.1.64`, `//@version 1.1.64`
- compat raw URL 확인 완료: `//@display-name ☸Eros Tower 1.1.64`, `//@version 4.0.15`
- 원격 브랜치: `main`
- 배포 커밋: `afc1b7cac80de476897d2662580533c4976c0304`
