# Eros Tower v1.1.40

## 로어 인지 엔진 수정

- 메인 주입 검색에서 원천 로어/설정/기억 청크가 `loreLedger`나 `psycheUnits`로 내부화된 뒤에만 잡히던 구조를 보강했다.
- `rankStateCandidates()`가 저장 상태 후보와 함께 현재 런타임 원천 source chunk 후보도 같은 검색 풀에서 평가하게 했다.
- 큰 로어북 하나 안에 여러 인물/설정이 들어 있는 경우에도, 요청어와 맞는 청크가 직접 후보로 올라와 `Retrieval Pack`에 들어갈 수 있게 했다.
- 별도 안전주입층이나 새 지침 블록을 추가하지 않았다. 기존 검색/후보/주입 엔진 경로 안에서 처리한다.

## 로어 본문 전달 수정

- `summarizeLedgerText()`가 로어/지식/내부화 유닛의 `summary`, `verbatimExcerpt`, `rawExcerpt`를 제목처럼만 쓰고 실제 본문을 잃는 문제를 수정했다.
- `Source Registry`와 기존 Control Floor의 source fallback 라인이 라벨/해시만 남기지 않고 요약 본문을 함께 전달하게 했다.
- 결과적으로 “로어가 선택됐는데 메인이 실제 설정 내용을 못 보는” 상황을 줄였다.

## 검증

- `node --check ErosTower.v1.update.js` 통과.
