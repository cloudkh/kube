Eureka and Zuul 설명
------
이번 시간은 Registry Service와 Proxy Service를 설정하는 방법에 대하여 기술하고자 한다.  
`Eureka` 는 netflix OSS에서 개발한 Registry Service libary 이다.  
`Zuul` 은 netflix OSS에서 개발한 Proxy Service libary 이다.  
위의 libary를 사용하여 손쉽게 Registry server와 Proxy server를 구성하는 방법을 설명한다.  

> spring cloud 는 spring boot + library 로 구성된 프로젝트들의 모음  
> 여기서 사용하는 예제는 아래의 github 에서 다운로드 가능하다.  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa 

### Eureka
먼저 다운로드된 소스코드의 registry-service 를 살펴보자.  
https://github.com/uengine-oss/msa-tutorial-class-management-msa/tree/master/registry-service  
파일의 구조는 간단하다.  
`RegistryServiceApplication` 과 `application.yml` 두개이다.  

#### RegistryServiceApplication.java
```java
@SpringBootApplication
@EnableEurekaServer
public class RegistryServiceApplication {
    public static void main(String[] args) {
        new SpringApplicationBuilder(RegistryServiceApplication.class).web(true).run(args);
    }
}
```
* @SpringBootApplication : 일반적인 스프링 부트 어플리케이션과 같다.  
* @EnableEurekaServer : 이 서비스가 Registry server 를 만드는 Annotation이다.  
이렇게 설정을 한 경우 추후 Registry server에 특정한 기능을 추가 하고 싶을때,  
source code level에서 컨트롤이 가능하다는 장점이 있다.  

기본적으로 spring-boot Application 의 커스터 마이징 레벨은 첫번째로 propertie 파일이다.  
여기 예제에서는 application.yml 파일을 썼는데, propertie 와 yml 파일 모두 우선으로 읽어들인다.  
최근 cloud computing 쪽은 yml 파일을 많이 사용중이다.  
yml 파일은 사람이 읽고 쓰기 좋은 형식으로 (json처럼) key and value 방식으로 depth를 줘서