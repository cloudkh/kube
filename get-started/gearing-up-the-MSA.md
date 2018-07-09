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

`mvn spring-boot:run`

을 하게되면 메이븐으로 부터 libary 를 다운 받은 후 기본 포트 8080으로 스프링 부트 어플리케이션이 구동되게 된다.
자신이 8080포트를 사용중이고, 다른 포트로 구동을 시키고 싶다면 아래와 같이 시작하면 된다.

`mvn spring-boot:run -Dserver.port=8081`

해당 서비스 확인하기
--------

콘솔창을 하나 더 연 후에, 해당 경로로 이동을 하여 httpie 로 확인을 해볼수 있다.
([Httpie-설치](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Httpie-설치))

```
$ http localhost:8080

{
    "_links": {
        "profile": {
            "href": "http://localhost:8080/profile"
        }
    }
}
```
http localhost:8080 를 치게되면 위에 처럼 profile 정보가 나오게되는데
web으로 치면 메인 페이지로 접근을 한 것이다.
얼굴이 없다 뿐이지, 데이터만 들어가 있다.

디자인 요소만 빼과, data 만 남긴것이 rest api 이다.

위의 메세지는 profile이라는 sub page가 있다는 뜻이다.


파일 상세 설명
-------
#### `src/SpringBootSampleApplication.java`

```java
@SpringBootApplication
public class SpringBootSampleApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringBootSampleApplication.class, args);
	}
}
```

main Class이다. 
> **TIP: @SpringBootApplication **
> 어노테이션으로 스프링부트 어플리케이션이라는 의미이다.
> 요즘에는 이런식으로 java class안에서 descriptive (기술적인), declarative(선언적) 방식이 유행하고있다. - 예전에는 xml 에서 선언하였다.

#### `pom.xml`

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.0.3.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>
   ....
</dependencies>
```
parent 에서 스프링 부트가 가지고 있는 기본적인 속성들을 가져온다.  
최초 프로젝트 생성시 설정하였던 H2, JAP등이 있는것을 확인 할 수 있다.