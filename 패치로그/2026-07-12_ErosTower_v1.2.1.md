# Eros Tower v1.2.1 Release

## 배포 정보

- 제품 버전: `1.2.1`
- v1 업데이트 비교 버전: `1.2.1`
- 4.x 호환 업데이트 비교 버전: `4.0.29`
- 기준 소스: `ErosTower.v1.artifact-test.js` `1.2.0-artifact-test.41`
- 백업: `D:\리수작업\에로스 타워\백업\ErosTower_before_v1.2.1_release_20260712-013327`

## 주요 변경

1. Wellspring/챈섭, NovelAI, ComfyUI, 커스텀 JSON 이미지 연결을 형식별 고정 슬롯으로 분리했다.
2. 이미진씨가 선택한 이미지 형식이 대응 연결을 자동 사용하도록 연결 선택과 요청 형식을 일치시켰다.
3. 기존 `1.2.0` 이미지 URL, API 키, 헤더와 이미진씨 모델 설정을 설정 스키마 4로 자동 이전한다.
4. 에이전트와 임베딩이 다른 활성 Provider의 API 키를 잘못 차용하지 않도록 자격 증명 경계를 분리했다.
5. Vertex Gemini Provider에 Automatic/Flex 트래픽 선택과 global 리전용 Flex 헤더를 추가했다.
6. 이미지 연결 설정 확인과 실제 이미지 1장 호출 테스트를 분리했다. NovelAI/챈섭/커스텀은 비표준 탐색 요청 없이 설정만 확인하고, ComfyUI는 `/system_stats`를 확인한다.
7. 삽화를 선택 문단 앞/뒤에 배치할 수 있고, 기본은 문단 앞이며 원문 병행 번역에서는 원문과 번역 사이를 가르지 않는다.
8. 채팅 삽화의 표시 크기, 원본 크기, 비율, 맞춤 방식을 이미진씨 설정에서 선택할 수 있게 했다.
9. NAI 기본 프리셋에 얼헌/ChestnutFlower 작화 프리셋을 추가하고 기존 NAI Vibe/Reference와 형식별 프리셋을 유지했다.
10. 이미지 생성 실패는 채팅 본문을 오염시키지 않고 진행 상태와 Run Log에만 기록한다.

## 정식 승격

- 제품 저장 접두사 `eros_tower_v02:`를 유지해 기존 설정, 상태, 기억, 앨범 데이터를 이어받는다.
- UI ID `eros-tower-v03-*`를 유지한다.
- 이미지 API 설정 스키마만 2에서 4로 이전하며, 무관한 Provider/에이전트/사용자 설정은 보존한다.
- 이미지 프롬프트 리비전은 `v1.2.1-image-placement-ko-v1`로 갱신했다.
- 테스트 전용 표시명, 저장 접두사, UI ID, update URL과 패치 문구는 정식 파일에 포함하지 않았다.
- 과거 채팅 삽화 자동 숨김 기능은 이번 테스트 기준 소스에 없으므로 포함하지 않았다.

## 로컬 검증

- `node --check` 정식 3파일 통과.
- ESLint `no-undef` 정식 3파일 통과.
- `git diff --check` 통과.
- 내장 무인자 debug 테스트 60개 실행, 예외 0개.
- 이미지 핵심 fixture의 모든 계약 참: 연결 고정, 인증 보존/격리, Vertex Flex, 문단 배치, 원문 병행 위치, 표시 크기/비율, 프롬프트 마이그레이션, NAI 프리셋/레퍼런스.
- NovelAI 연결 설정 확인 네트워크 호출 0회, 실제 이미지 호출은 `POST` 문자열 JSON 1회 및 이미지 바이트 반환 확인.
- ComfyUI 연결 확인은 `/system_stats` `GET` 확인.
- `1.2.0` 설정 마이그레이션에서 NAI URL·키·헤더·이미진씨 모델과 무관 설정 보존 확인.
- `ErosTower.v1.update.js`와 `☸에로스 타워.js` 바이트 동일.
- `ErosTower.update.js`는 상단 5줄을 제외한 본문이 v1 파일과 동일.
- 정식 파일의 테스트 전용 식별자 잔존 없음.

## 파일 해시

- `ErosTower.v1.update.js`: `5165FF9166B3C61D607E139F005D04104E65810B6F328384CC1F806058729581`
- `☸에로스 타워.js`: `5165FF9166B3C61D607E139F005D04104E65810B6F328384CC1F806058729581`
- `ErosTower.update.js`: `F9D91E58FED749B181708D193D062CD625DEFF30C6FFBA9B2115E035A4B3048B`

## 배포 후 확인

- 배포 커밋: 확인 후 기록
- v1 raw URL: 확인 후 기록
- compat raw URL: 확인 후 기록
