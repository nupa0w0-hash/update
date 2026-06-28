# Shared plugin update bridge

이 저장소는 더 이상 슈바봇의 메인 업데이트 채널이 아닙니다.

## SuperVibeBot

- 현재 최신 버전: `1.5.0`
- 슈바봇 전용 업데이트 저장소:

```text
https://github.com/nupa0w0-hash/supervibebot-update
```

- 권장 업데이트 URL:

```text
https://raw.githubusercontent.com/nupa0w0-hash/supervibebot-update/refs/heads/main/SuperVibeBot.update.js
```

## 1.4.8에서 업데이트 알림이 안 뜨는 경우

`1.4.8` 설치본은 예전 jsDelivr CDN 주소를 업데이트 URL로 사용합니다.
그래서 이 저장소에는 기존 사용자를 전용 레포로 넘기기 위한 브릿지용 `SuperVibeBot.update.js`만 남겨둡니다.

새로 설치하는 사용자는 전용 저장소의 raw URL을 사용하는 설치본을 받아야 합니다.

## 버전 운영 기준

- `1.4.9` 다음 공개 버전은 `1.5.0`입니다.
- 이후 실제 코드, 기능, 버그 수정이 배포될 때만 버전을 올립니다.
- 링크 검증, 문서 수정만으로는 버전을 올리지 않습니다.
- 작업 중간본은 공개 버전으로 취급하지 않고, 최종 설치본만 버전 릴리즈로 취급합니다.
