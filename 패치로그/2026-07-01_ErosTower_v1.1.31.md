# Eros Tower v1.1.31

## 변경 사항
- `psycheSources`, `psycheChunks`, `psycheUnits` 상태 레이어를 추가했습니다.
- 캐릭터 카드, 첫 메시지, 로어, Persona, Author Note, 장기 채팅 chunk를 사이키 소스 레지스트리로 동기화합니다.
- 사이키 메인 에이전트가 pending source chunk를 durable retrieval unit으로 흡수하는 ingest 경로를 추가했습니다.
- 기존 `setting_blocks`, `state_json`, `chat_history` 슬롯이 원문 덩어리 대신 compiled setting view, compact state JSON, compact history를 사용하도록 변경했습니다.
- 메인 브리핑의 Control Floor / Active Lore Bridge가 상태와 사이키 단위 요약을 우선 사용하도록 변경했습니다.
- 기존 long-memory cold-start는 새 사이키 source ingest 경로가 존재하면 중복 실행하지 않도록 처리했습니다.

## 의도
- 큰 시뮬봇/로어북에서 원문 자료가 메인 요청과 에이전트 요청에 통째로 들어가 응답이 멈추는 문제를 줄입니다.
- 자료 총량을 잘라내는 방식이 아니라, 소스 전체를 chunk 단위로 순차 처리하고 메인 주입에는 현재 턴에 필요한 compiled view를 넣는 구조로 변경했습니다.
- 에로스 에이전트 프롬프트, Source Handling 문구, 번역 프롬프트 모듈, README/작업 안내 문서는 변경하지 않았습니다.

## 검증
- `node --check update_repo\ErosTower.v1.update.js`
- `git diff --check`
