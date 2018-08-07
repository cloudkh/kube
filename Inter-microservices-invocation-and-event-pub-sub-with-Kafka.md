Inter microservices invocation
------
microservices 들 간의 내부 호출(invocation) 방법은 여러가지가 있다.  

1. 외부에서 호출을 하듯이 REST API 를 호출하는 방법  
2. FeignClient 를 통하여 바로 서비스를 접근방법(spring-cloud)  
3. Publish–subscribe pattern 을 사용한 Event Driven 방법  


1번 방법은 Spring에서 예를 들자면 `@RestController` 와 `@RequestMapping` 을 사용한 호출 방법이다.  
외부 서비스들을 연결할때 많이 쓰이는 방법이기에, 내부시스템도 물론 사용이 가능하지만,  
각 microservices 의 ip 및 domain 을 기억해야 한다.  

2번 방법은 `@FeignClient("서비스명")` 을 사용한 방법인데,  
여기서 서비스명은 eureka 에 등록이 되어있어야한다.  
이 방법은 기존에 설명 하였던 [[Integration with Feign Client]] 와 [spring-cloud-netflix-feign-tutorial](http://www.javainuse.com/spring/spring-cloud-netflix-feign-tutorial) 에서 자세한 설명을 참고할 수 있다.  
이 방법은 해당 마이크로 서비스를 가지고 와서 local 객체처럼 사용 할 수 있는 장점이 있다.  

이번시간에 집중할 내용은 3번 방법이다.  




