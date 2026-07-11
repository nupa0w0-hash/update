# Eros Tower v1.2.0 Release

## 배포 정보

- 제품 버전: `1.2.0`
- v1 업데이트 비교 버전: `1.2.0`
- 4.x 호환 업데이트 비교 버전: `4.0.28`
- 기준 소스: `ErosTower.v1.artifact-test.js` `1.1.85-artifact-test.33`
- 백업: `D:\리수작업\에로스 타워\백업\ErosTower_before_v1.2.0_release_20260711-192104`

## 정식 승격

- 테스트 플러그인의 기능 본문을 정식 v1 파일로 승격했다.
- 제품 저장 접두사는 기존 정식판과 같은 `eros_tower_v02:`를 유지해 설정, 관리상태, 장기기억과 채팅별 데이터를 이어받는다.
- 설정/채팅/햄버거 UI ID는 기존 정식판의 `eros-tower-v03-*`를 유지한다.
- 테스트 전용 이름, update URL, 저장 접두사, UI ID, 표시명과 내부 패치 문구를 제거했다.
- 이미지 resident 프롬프트 리비전은 정식 `v1.2.0-visual-reference-v1`로 전환했다.
- `ErosTower.v1.update.js`와 `☸에로스 타워.js`는 바이트 단위로 동일하다.
- `ErosTower.update.js`는 상단 호환 헤더 두 줄 외 본문이 v1 파일과 동일하다.

## 주요 변경 1~12

1. 이미진씨 이미지 resident와 장면 선택·프롬프트 계획 추가.
2. Wellspring, NovelAI, ComfyUI, custom 이미지 API/프리셋 분리 및 NAI reference/vibe 지원.
3. 생성 삽화의 문단 위치 삽입과 캐릭터별 프롬프트·외형 연속성 지원.
4. 채팅 귀속 이미지와 모바일 4~5열 인벤토리·중앙 정렬 사진 상세 화면의 앨범 추가.
5. 번역 표시본과 내부 canonical 본문을 분리해 번역이 기억·상태에 섞이지 않게 처리.
6. 현재 턴 필수 원천 우선 Psyche annotation, 체크포인트 저장, 순차 background backlog 적용.
7. 메인·Eros·Psyche가 공유하는 turn evidence와 원천·상태·기억 activation frame 적용.
8. 현재 인물, 비밀, 미래, knownBy/cannotKnow와 role-aware offscreen state 권한 분리.
9. Risu 길이 토글 감지 교정과 빈 pre-agent/translation 응답 재시도 적용.
10. 런타임 인덱스, state commit 직렬화·검색 재사용, 개별 스냅샷 저장으로 병목 축소.
11. 100/300턴 장기기억 회수와 사용자 설정/Provider 전체 백업·병합 복원 유지.
12. 원천 분석, 에이전트, 이미지, state commit 진행률과 진단 패키지 보강.

상세 사용자 공지:

- `D:\리수작업\에로스 타워\작업메모\에로스_타워_1.2.0_업데이트_공지_복붙용_20260711.md`

## 로컬 검증

- `node --check ErosTower.v1.update.js`: 통과
- `node --check ErosTower.update.js`: 통과
- `node --check ☸에로스 타워.js`: 통과
- `git diff --check`: 통과
- 내장 0인자 debug 테스트: 54개 실행, 예외 0
- 번역 표시/내부 원문 복원 fixture: 통과
- 앨범 단일 인벤토리, 모바일 4~5열 CSS, 상세 제목-사진-글귀 순서와 비백색 배경 fixture: 통과
- canonical state activation fixture: background-only 메인 승격 차단, direct/state-bridge 승격, World/Synthesis exact-linked state 허용, 무관 state 제외 확인
- large annotation fixture: canonical 1,762 units, foreground 16, background 1,746, foreground 1 batch, 원문 즉시 사용과 role selection cache 재사용 확인
- state commit transport fixture: 2 calls, 1 retry, embedding call 0, main turn evidence 재사용 확인
- 개별 snapshot body 저장/복원 fixture: manifest v2, payload 분리와 turn/memory 복원 확인
- 테스트 전용 문자열 잔존: 0
- v1/직접 설치 파일 SHA-256 동일 확인
- compat 본문 diff: 0

## 파일 해시

- `ErosTower.v1.update.js`: `B4D3D8AF1E8724DAE9E7913ED0D25D58864353AAA6F17FF5E5B988222AB1853F`
- `☸에로스 타워.js`: `B4D3D8AF1E8724DAE9E7913ED0D25D58864353AAA6F17FF5E5B988222AB1853F`
- `ErosTower.update.js`: `EE6F797100BA3C50A85E6DA01D9D093D4BBDD2D3633E5197D3A54B6701A2C24C`

## 배포 후 확인

- 배포 커밋: `852b2936fd94937cd2db229e842b15b205045234`
- 확인 시각: `2026-07-11 19:40 KST`
- v1 raw URL: HTTP 200, `//@display-name ☸Eros Tower 1.2.0`, `//@version 1.2.0`
- v1 raw SHA-256: `B4D3D8AF1E8724DAE9E7913ED0D25D58864353AAA6F17FF5E5B988222AB1853F`로 로컬과 일치
- compat raw URL: HTTP 200, `//@display-name ☸Eros Tower 1.2.0`, `//@version 4.0.28`
- compat raw SHA-256: `EE6F797100BA3C50A85E6DA01D9D093D4BBDD2D3633E5197D3A54B6701A2C24C`로 로컬과 일치
- commit-SHA raw v1/compat: 두 파일 모두 HTTP 200 및 로컬 SHA-256 일치
