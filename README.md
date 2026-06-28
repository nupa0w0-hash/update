# SuperVibeBot update

슈바봇 자동 업데이트 배포용 저장소입니다.

## 최신 버전

- 현재 최신 버전: `1.4.12`
- 최신 설치 파일: `SuperVibeBot.auto.js`
- 업데이트 확인 파일: `SuperVibeBot.update.js`
- 권장 업데이트 URL:

```text
https://raw.githubusercontent.com/nupa0w0-hash/update/refs/heads/main/SuperVibeBot.update.js
```

## 1.4.8에서 업데이트 알림이 안 뜨는 경우

`1.4.8` 설치본은 예전 jsDelivr CDN 주소를 업데이트 URL로 사용합니다.
해당 CDN 경로가 edge 캐시 문제로 오래된 `1.4.8` 또는 `1.4.9` 파일을 반환할 수 있어, RisuAI가 최신 `1.4.12`를 인식하지 못할 수 있습니다.

이 경우 `SuperVibeBot.auto.js` 또는 최종 배포 폴더의 `🐸SuperVibeBot v1.4.12 자동업데이트.js`를 한 번 수동 재설치하세요.
`1.4.12`부터는 raw GitHub refs URL을 사용하므로 이후 업데이트 확인이 더 안정적입니다.

## 버전 운영 기준

- 실제 코드, 기능, 버그 수정이 배포될 때만 버전을 `0.0.1` 올립니다.
- CDN 캐시 확인, 링크 검증, 문서 수정만으로는 버전을 올리지 않습니다.
- 작업 중간본은 공개 버전으로 취급하지 않고, 최종 설치본만 버전 릴리즈로 취급합니다.
