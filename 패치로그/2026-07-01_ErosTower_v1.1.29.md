# Eros Tower v1.1.29

## 1.1.29 final additions

- Translation agent runtime now strips a trailing ChatML `assistant`/`model` prefill block before calling the provider.
- Translation agent now retries only empty provider outputs. The retry count is configurable next to the translation prompt selector and defaults to `1`.
- Timeout defaults and timeout UI limits were not changed.
- If every translation attempt is empty, the run records `translation-empty-output` instead of silently treating it as a successful no-op.

## 날짜

2026-07-01

## 변경 목적

- 전처리 에이전트가 로어/기억/관리자료를 참고한 뒤, 해당 자료를 다시 메인 전달용 노트에 재전시하는 경향을 줄인다.
- 메인 모델에는 Eros Tower core가 관련 source context를 별도로 주입한다는 전제를 pre-agent에게 명시한다.
- 별도 API 호출, 증류 에이전트, source echo 제거 필터는 추가하지 않는다.

## 수정 내용

- 플러그인 메타 버전을 `1.1.29`로 올렸다.
- `pre` 에이전트 시스템 프롬프트에만 다음 공통 Source Handling 문구를 런타임으로 추가한다.

```text
Source Handling:
The final-response model receives Eros Tower source context separately. Use lore, memory, state, retrieved context, character cards, and chat history as shared working context for your active agent role, not as material to re-deliver.
```

- 번역 에이전트, 후처리 에이전트, State Committer에는 해당 문구를 추가하지 않았다.
- `EROS_AGENT_PROMPT_REVISION`은 올리지 않았다. 기존 사용자의 저장 프롬프트를 기본값으로 강제 리셋하지 않기 위함이다.

## 발행 상태

- 로컬 파일만 갱신했다.
- GitHub commit/push 및 update raw 발행은 하지 않았다.

## 검증

- `node --check update_repo\ErosTower.v1.update.js` 통과
- `node --check 완성본\에로스 타워 v0.3\eros-tower-v0.3.js` 통과
- `node 완성본\에로스 타워 v0.3\tests\regression-v0.3.js` 통과
- `node 완성본\에로스 타워 v0.3\tests\smoke-v0.3.js` 통과
- `node 완성본\에로스 타워 v0.3\tests\session-sim-v0.3.js 20` 통과
- `node 완성본\에로스 타워 v0.3\tests\lorebook-opening-output-sim-v0.3.js` 통과
