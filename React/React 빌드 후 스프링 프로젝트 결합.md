
## 빌드 후 Build 파일 이동시키는 스크립트 추가

`package.json` 파일에 빌드 후에 실행될 스크립트를 추가
`build` 스크립트가 성공적으로 완료된 후 `move-build`라는 새 스크립트를 실행하도록 설정
빌드된 파일을 스프링부트 프로젝트의 `static` 디렉토리로 이동시킴
    
Windows 환경의 경우, 다음과 같이 `package.json`에 스크립트를 추가
`robocopy` 명령어를 사용

```json
"scripts": {
  "build": "react-scripts build",
  "move-build": "robocopy ./build ../src/main/resources/static /E /XD node_modules /NFL /NDL /NJH /NJS /nc /ns /np && exit 0",
  "postbuild": "npm run move-build"
}
```


리눅스나 macOS에서는 `rsync` 또는 `cp` 명령어를 사용

## 정적 리소스

`React프로젝트루트/public/` 아래에 위치 시킨다.
동적으로 경로가 변하는 환경에서도 %PUBLIC_URL%/파일명으로
상대경로 지정이 가능하다.