# Eros Tower v1.1.43

## lore engine

- 원천 로어를 LLM 추출 결과와 별개로 즉시 사용 가능한 canonical unit으로 등록한다.
- 큰 원천 로어는 단일 요약으로만 보존하지 않고, 구조적 line/block 기준으로 deterministic segment canonical unit을 만든다.
- 각 segment canonical unit은 같은 원천의 `parentSourceId`, `segmentId`, `segmentIndex`, `segmentCount`, `segmentHash`, `siblingIds`를 가진다.
- 사이키 ingest 결과는 원천 canonical unit을 대체하지 않고, 별도 `psycheUnits`로 해석과 연결을 더하는 enrichment로 유지된다.

## recall selection

- 후보 선택은 generic score만 따르지 않고 recall stage를 먼저 본다.
- stage 순서:
  - foundation / always
  - trigger
  - active memory bridge
  - recursive
  - sibling completion
  - generic
- 선택된 canonical segment와 같은 원천의 sibling segment를 completion 단계에서 보완한다.

## injection routing

- 메인 주입은 system append만 고정하지 않는다.
- 프롬프트 안의 Eros context placeholder가 있으면 그 위치에 치환한다.
- placeholder가 없으면 마지막 user message prefix에 주입한다.
- user message가 없을 때만 system message로 fallback한다.

## verification

- `node --check ErosTower.v1.update.js` 통과.
