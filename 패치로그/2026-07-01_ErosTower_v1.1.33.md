# Eros Tower v1.1.33

## 핵심 변경
- 원천 로어북을 메인/에이전트 프롬프트에 별도 원문 후보로 직접 싣지 않고, 내부 자료망 흡수와 출처 추적용으로 사용하도록 정리했다.
- `[Canonical Lore Candidates]`와 `[Source Registry]`는 원문/긴 요약 대신 종류, 경로, 키, 우선도, 해시 같은 메타 정보만 표시한다.
- 메인 브리핑의 `0` 주입 예산을 무제한 이어붙임이 아니라 자동 구조화 브리핑으로 처리해 대형 상시 로어북에서 요청이 과도하게 커지는 문제를 줄였다.

## 자료망 개선
- canonical lore에 `sourceClass`, `loreStability`, `retention`을 추가해 불변/상시/선택/문맥 로어를 내부적으로 구분한다.
- `constant`, 캐릭터 설명, 첫 메시지는 foundation/fixed로 보존성이 높게 처리한다.
- 일반 always-active 로어는 모두 강제 주입하지 않고, 현재 입력, 현재 인물, 장면, 관리상태, 장기기억과의 관련성에 따라 우선순위가 달라지게 했다.
- `Active Lore Bridge`를 `Current Weave Briefing`으로 정리해 캐논, 관리상태, 장기기억, 비밀, 관계가 현재 턴 기준으로 함께 올라오게 했다.

## 유지 사항
- 에로스 에이전트 4역할과 `Source Handling` 문구는 유지했다.
- 별도 로어북 토큰 제한 UI는 추가하지 않았다.
- README와 작업 안내 문서는 수정하지 않았다.
