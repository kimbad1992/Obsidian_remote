Spring Legacy 프로젝트에서 [Maven Repository](https://mvnrepository.com/)에 등록되어있지 않은 라이브러리(jar 파일)를 사용할 때, 프로젝트 빌드에서 에러가 나는 경우가 있었다.

해당 jar들을 한 폴더에 모아놓고 인텔리제이 -> Project Structure 설정에서
그 폴더를 통째로 **전역 라이브러리로 등록** 하거나, 해당 프로젝트의 **특정 모듈, 라이브러리로 등록**하거나 하는 방법을 사용해봤는데, 프로젝트 빌드를 한번 이상 진행한 뒤, 해당 라이브러리들은 pom.xml에 등록되어있지 않아서 계속 설정이 초기화 되는 문제가 발생했다.

그래서 jar들을 한 폴더에 모아놓고 해당 jar들을 pom.xml에 임의의 dependency로 추가하는 방법을 사용했다.

Maven Repository에 등록되어있지 않은 특정 라이브러리 jar파일들을 전부

`프로젝트_루트경로/extern_lib(폴더 명은 마음대로)`에 집어넣고

```xml
...
<dependency>  
    <groupId>com.extern(임의의 그룹ID 지정)</groupId>
    <artifactId>someLibrary</artifactId>  
    <version>1.0.0(임의의 버전 지정)</version>  
    <scope>system</scope>  
    <systemPath>${project.basedir}/extern_lib/someLibrary.jar</systemPath>  
</dependency>
```
를