# Eros Tower v1.1.38

## Psyche Weave Anchor Graph
- `Psyche Weave`에 인물/장소 앵커 노드를 추가했습니다.
- 같은 인물, 장소, 관점, 비밀, 관계가 서로 다른 원천 로어/기억/관리상태에서 나와도 하나의 활성 축으로 묶이도록 했습니다.
- 후보 노드는 앵커와 `self`, `alias`, `participant`, `present`, `place`, `owner`, `known-by`, `cannot-know`, `suspects` 링크로 연결됩니다.

## 관점/비밀 상호작용 강화
- `knownBy`, `cannotKnow`, `owners`, `holders`, `suspecters`, 관계 참여자 정보를 Weave 앵커로 반영합니다.
- 기존 knowledge boundary 차단은 유지하면서, 비밀 자체는 내부적으로 연결되되 현재 관점에서 알 수 없는 항목은 전파되지 않도록 했습니다.

## Active Anchor Briefing
- 메인 모델과 에로스 에이전트에 들어가는 `[Psyche Weave]` 브리핑에 `[Active Anchors]` 섹션을 추가했습니다.
- 단순 노드 목록보다 먼저 “현재 활성화된 인물/장소/관점 축”을 보여줘, 메인 응답이 엑스트라를 만들기보다 기존 세계망을 따라가도록 했습니다.

## 진단
- 대시보드 자동 기억 엔진 패널에 `Weave 축` 카운트를 추가했습니다.
- Run Log 본문은 늘리지 않고 기존 숫자 요약 중심을 유지합니다.

## 의도
- 1.1.37의 Weave가 출처/키워드 중심이었다면, 1.1.38은 인물과 관점 중심의 내부 세계망에 더 가까워졌습니다.
- Agent!, 옴니노드, 위그로어의 단순 결합이 아니라 에로스 타워만의 관리상태/비밀/관점 기반 그래프 구조를 강화하는 단계입니다.
