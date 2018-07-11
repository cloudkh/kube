Spring MVC 기반 RPC
------

JPA 를 학습할적에 Entity 가 있는 경우 Repository 라는 서비스를 만들어 사용 하였다.  
그러나 특정 Shared Service 를 만들어 쓰는 경우는 Entity가 아니고,  
RPC(Remote procedure call) 에 가깝기 때문에   
JAX-RS 를 사용한 Interface를 정의하여 사용한다.  
> [ RPC:별도의 원격 제어를 위한 코딩 없이 다른 주소 공간에서 함수나 프로시저를 실행할 수 있게하는 프로세스 간 통신 기술이다. ]  
> [JAX-RS (Java API for RESTful Web Services): 는 자바 플랫폼에서 경량화된 REST 방식의 웹 애플리케이션 구현을 지원하는 자바 API이다. ]  

JAVA에서는 JAX-RS가 JAVA에서 제공하는 표준 API이지만 Spring Framework에서는 Spring MVC를 사용한다.  
아래는 Spring MVC 를 사용한 interface 예제이다.  

#### SharedCalendarService.java
```java
public interface SharedCalendarService {
    @RequestMapping(method = RequestMethod.GET, path="/calendar/{instructorId}/{date}")
    ResourceSupport getSchedules(
            @PathVariable("instructorId") Long instructorId, 
            @DateTimeFormat(pattern = "yyyy-MM-dd") @PathVariable("date") Date date);
}
```