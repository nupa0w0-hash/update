# Eros Tower 1.2.3

## 배포 범위

- 일반 업데이트 채널: `ErosTower.v1.update.js` / `1.2.3`
- 로컬 설치 파일: `☸에로스 타워.js` / `1.2.3`
- 기존 4.x 업데이트 호환 채널: `ErosTower.update.js` / `4.0.31`
- 승격한 테스트 기준본: `ErosTower.v1.artifact-test.js` / `1.2.1-artifact-test.51`
- 1.2.2 이후 테스트본에서 검증한 canonical 활성화·상태 권한 교정과 앨범 viewport 보정도 함께 포함한다.

## 확인한 이미지 문제의 실제 원인

1. 이미지 API 호출과 저장은 최근 진단에서 두 번 모두 성공했다. 장애의 중심은 연결 실패가 아니라, 에로스 타워가 이미지 모델 앞에 추가한 과도한 로컬 계약이었다.
2. 기존 계약은 `place`, `situation`, `visualAge`, `appearance`, `body`, `attire`, `expression`, `action`, 9칸 `position`을 각각 강제하고, 런타임에서 인원수·성별 focus·반대 성별 negative·좌표를 다시 추론했다.
3. NovelAI에는 완성된 `|` 프롬프트와 별도 V4 캐릭터 캡션·좌표를 동시에 만들었다. 같은 인물 정보를 서로 다른 구조로 중복 전달했고, 두 인물이 같은 좌표로 겹칠 수 있었다.
4. 생성된 과거 이미지의 외형 요약을 다음 이미지 계획에 다시 넣어 잘못 생성된 외형이 자기 증폭될 수 있었다.
5. 실제 RisuAI의 NovelAI 경로는 완성된 positive/negative 문자열을 받아 top-level 입력과 V4 base caption에 동일하게 넣고, `char_captions=[]`, `use_coords=false`로 보낸다.
6. 확인한 삽화·인레이 모듈도 공통 장면 문자열과 완성된 캐릭터 문자열을 조립할 뿐, 에로스 타워의 9칸 위치표·`visualAge`·자동 성별 negative를 요구하지 않는다.

## 변경

### 단순한 이미지 계획 계약

- 기본 출력 구조를 `paragraph`, `placement`, `camera`, `scene`, 선택적 `supplement`, `characters[].positive/negative`로 줄였다.
- 선택된 화풍 프리셋은 맨 앞에 한 번만 둔다.
- 공통 `camera + scene + supplement` 뒤에 완성된 캐릭터 positive를 순서대로 붙인다.
- 인원수·성별 focus·반대 성별 negative·9칸 position·좌표 변환을 삭제했다.
- NovelAI/Wellspring은 RisuAI와 동일하게 완성된 positive/negative를 top-level과 V4 base에 그대로 보내고, 별도 캐릭터 캡션과 좌표를 사용하지 않는다.
- ComfyUI/custom JSON은 NAI용 `|` 구분자를 사용하지 않고 쉼표 기반 positive/negative를 받는다.

### 호환 경계

- 사용자가 만든 기존 이미진씨 system prompt는 그대로 보존한다.
- 구형 custom 출력에 새 `scene` 또는 `positive`가 없을 때만 중앙 정규화기에서 `place + situation`, `label + visualAge + appearance + body + attire + expression + action`을 한 번 평탄화한다.
- 이 호환 변환은 기존 position, 좌표, 자동 focus, 자동 gender negative를 복원하지 않는다.
- 기본 내장 프롬프트는 구조화되지 않은 raw prompt가 strict 검증을 우회하지 못한다. custom prompt의 기존 raw prompt 출력은 계속 허용한다.

### 시각 근거와 저장

- canonical source의 안정된 신원·외형과 현재 관리 상태의 복장·부상·장소·시간만 Visual Ground에 제공한다.
- 생성 이미지 이력 재주입은 제거했다.
- RisuAI API v3 플러그인에는 모듈용 `generateImage`와 이미지 설정 읽기 API가 없으므로, 직접 provider 요청·인증·응답 해제·`saveAsset`·`additionalAssets` 등록 경계는 유지했다. 이를 제거하면 별도 companion module이 필요해 구조가 더 복잡해진다.

### 함께 승격한 기존 테스트본 교정

- 실제 사용자/assistant 채팅만 canonical 활성화 증거로 사용하고 시스템에 주입된 NPC 색인·로어 본문은 trigger 근거에서 제외한다.
- 직접 trigger, state bridge, recursive 연결과 sibling packing의 권한을 분리해 무관한 인물이 현재 상태·Visual Ground로 승격되는 경로를 막았다.
- 앨범 상세 이미지는 데스크톱·모바일에서 가로와 세로 viewport를 모두 넘지 않도록 보정했다.

## 로컬 검증

- 세 배포 파일 `node --check`: 통과
- 세 배포 파일 ESLint `no-undef`: 통과
- `git diff --check`: 통과
- 내장 무인수 진단 75개: 실행 75, 예외 0
- 이미지 관련 boolean 회귀검사: 실패 0
- 새 회귀검사:
  - RisuAI flat positive/negative와 V3/V4 envelope 일치
  - NAI sparse character negative 슬롯 보존
  - ComfyUI/custom positive·negative에 NAI pipe가 들어가지 않음
  - 좌표·자동 성별 negative·캐릭터별 V4 caption 미사용
  - 구형 custom 구조의 중앙 평탄화와 position 폐기
  - 내장 strict raw prompt 우회 차단과 custom raw prompt 호환
  - 현재 복장·부상 연속성 유지와 생성 이미지 이력 미주입
- `ErosTower.v1.update.js`와 `☸에로스 타워.js`: 전체 본문 동일
- `ErosTower.update.js`: 앞 5개 metadata 줄을 제외한 본문 동일

### 배포 후보 SHA-256

- `ErosTower.v1.update.js`: `ED85ABD628DDD0129A0827F0706DCB29FF86E88B8129F6E9F19115904D0B9F7F`
- `☸에로스 타워.js`: `ED85ABD628DDD0129A0827F0706DCB29FF86E88B8129F6E9F19115904D0B9F7F`
- `ErosTower.update.js`: `F3AEB7334416C0811EC70A5EC8366BE605B0A383F01592DD5A4AFF2AF9E2EE7A`
- 테스트 기준본 `.51`: `0AEC4DCF3C19609C26D878111DE9D8D2D83E982ABCF7750BA35F59D116D14EC0`

## 되돌림

- `.50` 테스트본 백업: `패치로그/backups/artifact-test.50-before-risu-flat-image-contract-20260713/ErosTower.v1.artifact-test.js`
- `.50` 백업 SHA-256: `453FD4F834F5254EEC5E38636547878B3078D8381467BD19130733B9EFCCB903`
- 1.2.2 배포본 백업: `../백업/ErosTower_before_v1.2.3_release_20260713-231533/`
- 1.2.2 일반/로컬 SHA-256: `5790C1A417DB2251C63679E6244A664DA09DC07DE5C71C296BC99FD6127BD85B`
- 1.2.2 호환 SHA-256: `9F8805900A08909DCA6F670CE7FB5474A0AD0EDF28C8E245DF3CC5ADE1E9F8C5`

## 원격 배포 검증

- 배포 커밋: 배포 후 기록
- `origin/main`: 배포 후 기록
- commit-pinned raw 파일과 로컬 SHA-256 비교: 배포 후 기록
- 실제 update URL의 metadata와 SHA-256 비교: 배포 후 기록
