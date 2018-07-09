기본적인 Micro Service를 Springboot로 시작하는 방법 입니다.
--------

http://start.spring.io/
사이트에 들어가서 스프링 부트의 기본 프로젝트 템플릿을 생성합니다.

Group과 Artifact를 입력하여 줍니다. 하이픈과 소문자를 사용하여 생성하는 경향이 있습니다.
Dependencies 에 
* JPA	- DB ORM
* Rest Repository - 기본 Hateoas
* H2	- 기본 내장 DB

를 입력하고, Generate Project를 클릭하여 줍니다.

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/1_1.png)

Artifact 에 입력한 프로젝트 명으로 zip파일이 다운로드 됩니다.

기본 프로젝트 구조
--------
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/1_2.png)

기본 maven프로젝트로 생성된 폴더 구조를 볼 수 있습니다.
여기서 mvnw 파일은 maven wrapper 로 메이븐이 아예 없는 사람이 있기때문에 메이븐 설치까지 해주는 파일입니다.
해당 프로젝트로 폴더를 이동하여

mvn spring-boot:run

을 하게되면 