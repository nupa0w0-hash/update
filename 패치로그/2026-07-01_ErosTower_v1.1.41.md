# Eros Tower v1.1.41

## 임시 변경 제거

- v1.1.40에서 추가했던 별도 원천 후보 경로를 삭제했다.
- 현재 코드는 기존 검색 후보와 Retrieval Pack 포맷 경로만 사용한다.
- v1.1.40 패치로그 파일도 현재 트리에서 제거했다.

## 유지한 핵심 수리

- `summarizeLedgerText()`가 선택된 로어/지식/내부화 유닛의 본문을 잃는 문제는 엔진 결함이므로 유지했다.
- 이제 `loreLedger`, `psycheUnits`, `knowledge` 등이 정상 후보로 선택되면 `summary`, `verbatimExcerpt`, `rawExcerpt`가 제목 뒤 본문으로 보존된다.
- 즉, 별도 주입층을 만들지 않고 기존 검색 후보와 Retrieval Pack 포맷 경로를 수리했다.

## 검증

- `node --check ErosTower.v1.update.js` 통과.
