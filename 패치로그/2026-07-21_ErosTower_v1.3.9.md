# Eros Tower 1.3.9 — canonical blob 세대 누적/OOM 수정

## 신고 및 재현

- 90턴 이상 진행한 세션에서 진행 1회당 `eros_tower_v02:canonical-blob:*` 키가 약 170개씩 증가했다.
- 기존 구현은 canonical unit을 고정 8개 단위로 저장했다. 재현 fixture 1,360개는 정확히 170개 블롭 세대가 된다.
- canonical source가 하나만 실제 변경되어도 모든 unit의 `lastSeenTurn`을 현재 턴으로 덮어썼다. 따라서 170개 블롭의 내용 해시가 매번 전부 바뀌었다.
- 새 참조를 커밋한 뒤 이전 세대 블롭을 회수하지 않아 진행할 때마다 한 세대가 통째로 남았다.

## 수정

1. 내용이 바뀌지 않은 canonical unit은 저장 표현과 `lastSeenTurn`/`updatedAt`을 그대로 유지한다.
2. 고정 8개 slicing을 unit identity 기반 content-defined boundary(최소 4개, 최대 12개)로 교체했다.
   - 내용 수정은 해당 청크만 바꾼다.
   - 중간 삽입/삭제도 다음 안정 경계까지만 영향을 준다.
3. canonical 블롭 쓰기 → scope 참조 교체 → 미참조 블롭 GC를 전역 큐에서 직렬 처리한다.
   - 다른 scope가 참조 중인 공유 블롭은 기존 retain 검사로 보존한다.
   - 참조 스캔이 하나라도 실패하면 삭제 전체를 건너뛰는 기존 fail-safe도 유지한다.
4. scope를 런타임에서 처음 attach할 때 GC를 한 번 실행해 1.3.8 이하에서 이미 쌓인 고아 세대를 자동 회수한다.
5. canonical store가 비었을 때도 참조 삭제와 블롭 GC를 같은 직렬 작업으로 처리한다.

## 정량 검증

1,360 canonical unit / 90턴 fixture에서:

- 최초 현 세대: 163개 블롭
- unit 1개 수정: 162/163개 재사용, 물리 블롭 총 163개
- unit 1개 중간 삽입: 161/163개 재사용
- 의도적 dirty save 총 10회: 물리 블롭 최고 163개
- 변경 없는 다음 턴: canonical cache dirty=false, 추가 저장 없음
- 미참조 이전 세대 블롭 주입 후 scope attach: 자동 회수됨

기존 고정 청크 형식으로 330개가 누적된 실제 형태라면 첫 attach GC 후 현재 참조 세대 약 170개만 남는다. 다음 실제 canonical 변경 시 새 청크 형식으로 전환되어 이 fixture 기준 163개에 수렴하며, 이후 턴 수에 비례해 세대가 누적되지 않는다.

## 호환성과 안전성

- 저장 reference version과 blob body schema는 변경하지 않았다. 1.3.8 이하 참조를 그대로 읽을 수 있다.
- unit 순서, checksum 검증, scope별 reference, 공유 blob 보존 규칙을 유지했다.
- 상태/기억/스냅샷 저장 구조는 변경하지 않았다.

## 검증 결과

- 정식판 1.3.9 / 호환 업데이트 채널 4.0.46
- 세 파일 `node --max-old-space-size=64 --check` 통과
- 정식판·호환판 각각 198개 테스트, 1,657개 불리언, 예외 0
- true 1,634 / 기존 진단성 false 23; 신규 false 0
- ESLint `no-redeclare`, `no-dupe-keys` 통과
- `ErosTower.v1.update.js`와 `☸에로스 타워.js` byte-identical
- `ErosTower.update.js`는 version/update-url 헤더만 다름

## 백업

- `패치로그/backups/20260721-1.3.9-canonical-blob-churn/`
