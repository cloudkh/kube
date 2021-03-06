Eureka and Zuul 설명
------
이번 시간은 Registry Service와 Proxy Service를 설정하는 방법에 대하여 기술하고자 한다. 

### 참고
`Registry` -> 많은 수의 서비스들간의 앤드포인트를 찾고, 서비스의 상태를 어딘가에 등록하는 기능.  
`Proxy` -> 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 기능.  
`Eureka` -> netflix OSS에서 개발한 Registry Service libary.  
`Zuul` -> netflix OSS에서 개발한 Proxy Service libary.  
위의 libary를 사용하여 손쉽게 Registry server와 Proxy server를 구성하는 방법을 설명한다.  

> spring cloud 는 spring boot + library 로 구성된 프로젝트들의 모음  
> 여기서 사용하는 예제는 아래의 github 에서 다운로드 가능하다.  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa 

### Eureka
먼저 다운로드된 소스코드의 registry-service 를 살펴보자.  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa/tree/master/registry-service   
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
* @EnableEurekaServer : 이 서비스가 Registry server 라고 설정해주는 Annotation 이다.  
이렇게 설정을 한 경우 추후 Registry server에 특정한 기능을 추가 하고 싶을때,  
source code level에서 컨트롤이 가능하다는 장점이 있다.  

기본적으로 spring-boot Application 의 커스터 마이징 레벨은 첫번째로 properties 파일이다.  
여기 예제에서는 application.yml 파일을 썼는데, properties 와 yml 파일 모두 우선으로 읽어들인다.  
최근 cloud computing 쪽은 yml 파일을 많이 사용중이다.  
yml 파일은 사람이 읽고 쓰기 좋은 형식으로 (json처럼) key and value 방식으로 
depth를 주어서 property를 관리하기가 편하다.

#### application.yml
```yml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
  server:
    enableSelfPreservation: false
```
위의 server, port 부분을 properties 파일로 변경한다면  
`server.port=8761` 이런식으로 변경이 가능하다.  

마이크로 서비스는 Registry 가 항상 있어야 한다.  
그리고 Registry를 인식하기 위하여 Registry 주소는 각 서비스에서 가지고 있어야 한다.  
아래는 다른 마이크로 서비스에서 Registry 주소를 설정한 예제이다.  
```yml
eureka:
  client:
    enabled: true
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
    healthcheck:
      enabled: true
```
설정의 여러 옵션이 있지만 최소한의 옵션만 설정 하였고, 각 설정값을 설명하자면  
eureka.client.enabled : true / false -> 유레카 클라이언트를 등록/해제 하겠다.  
eureka.client.defaultZone : 유레카 서버 주소  
eureka.client.healthcheck.enabled : 유레카 서버의 상태를 계속 살펴보겠다.  
> 참고  
> https://cloud.spring.io/spring-cloud-netflix/multi/multi__service_discovery_eureka_clients.html

#### Registry Server를 설정할때 가장 중요한 점은 cluster 로 구성을 해야한다.  
그 이유는 Registry Server가 죽으면 마이크로 서비스가 연결이 아예 안되기 때문이다.  
eureka 로 cluster 를 하기 가장 쉬운 방법은 eureka server를 여러대 띄운 후,  
각 마이크로 서비스의 eureka 설정에서 아래와 같이 주소를 여러개 나열하는 방법이다.  
`defaultZone: http://127.0.0.1:8761/eureka/,http://127.0.0.1:8762/eureka/`

### Zuul
다운로드된 소스코드의 proxy-service 를 살펴보자.  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa/tree/master/proxy-service    
eureka와 마찬가지로 파일의 구조는 간단하다.  
`GatewayApplication` 과 `application.yml` 두개이다.  

#### GatewayApplication.java
```java
@EnableZuulProxy
@EnableDiscoveryClient
@EnableEurekaClient
@SpringBootApplication
public class GatewayApplication {
  public static void main(String[] args) {
    SpringApplication.run(GatewayApplication.class, args);
  }
}
```
* @SpringBootApplication : 일반적인 스프링 부트 어플리케이션과 같다.  
* @EnableZuulProxy : 이 서비스는 Proxy server 라고 설정해주는 Annotation 이다.  
* @EnableEurekaClient : 이 서비스는 Registry client 라고 설정해주는 Annotation 이다.    

`@EnableDiscoveryClient` 는 `@EnableEurekaClient`가 있다면 사용할 필요가 없다.  
`@EnableDiscoveryClient` 를 사용하여 Eureka에 client로 등록한 것이 `@EnableEurekaClient`다.  
 
#### application.yml
```yml
zuul:
  ignored-headers: Access-Control-Allow-Credentials, Access-Control-Allow-Origin
  addProxyHeaders: true

  routes:
    courses:
      path: /courses/**
      serviceId: course
      stripPrefix: false
```
설정 파일을 살펴보면 zuul.routes 을 하기위하여 courses 라고 하는 path로 들어올적에  
eureka에 등록되어있는 course 라는 서비스를 찾아서 redirect 해달라는 의미이다.  
stripPrefix 는 routing 하기 전에 경로에서 접두어를 제거할지 여부를 나타내는 플래그다.  
그러나 zuul은 이와같은 routes 설정이 없어도 기본적으로 routing이 가능하다.  
url의 첫번째 path는 serviceId로 인식을 하는것이 zuul의 기본 설정이다.  
zuul.routes 설정을 하면 아래와 같이 두벌의 path가 만들어 지는 것이다.  
```
http localhost:8080/course/courses  
http localhost:8080/courses 
```

그러나 zuul은 front-end 에서 접근을 하는 서비스 이기때문에, zuul.routes를 설정 안하고,  
`localhost:8080/course/courses` 이와 같이 쓴다면 기존의 monolith 에서 사용하던  
path와 완전이 달라지게 된다. 그래서 일일이 써주는게 좋은 방법이다.  

> http://cloud.spring.io/spring-cloud-netflix/multi/multi__router_and_filter_zuul.html  
> 위의 url로 접근하면 netflix에서 zuul을 어떻게 사용하는지 나온다.  
> 인증, Stress Testing, Canary Testing(A/B test 처럼 기존버전과 새버전의 호출하는 api가 똑같이 반응하는지 테스트) 등이 있다.  