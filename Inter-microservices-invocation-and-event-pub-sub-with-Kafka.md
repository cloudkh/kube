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
1번과 2번 방법은 호출 기반 아키텍처(Request-Driven Architecture)다. 이와 같이 설계시 서비스를  
교체시 영향을 미치는 요소가 많을 수 밖에 없다.  
3번 방법은 이벤트 중심 아키텍처(EDA, Event-Driven Architecture)인데, 이는 이벤트가 발행(Publish)되고,  
이 이벤트가 필요한 서비스에서 이벤트를 수신(subscribe)하여 사용하는 방법이다.  

쉬운 예를 들자면,  
쇼핑몰에서 고객이 물건을 `주문`하고 나면, `포인트`,`업적`,`축하메일`,`배송`,`장바구니` 등의 서비스가  
연달아 실행을 해야 한다고 가정을 하자. 이를 호출기반 아케틱쳐로 구현을 한다면, 5개의 서비스의  
서비스명이나, doamin 명 등을 모두 알고 있어야 하고, 모두 call을 해야한다.  
이것을 event pub sub 으로 변경한다면, `주문`서비스에서는 이벤트를 발행만 하고,  
나머지 서비스에서는 수신만 하여, 이벤트가 발생 후 해야할 일만 각자 서비스에서 준비하면 된다.  
여기에 microservice 들이 추가된다고 하여도, 기존 시스템에 미치는 영향이 최소화 된다.  

Event Publish–subscribe with Kafka
-----
이벤트를 주고 받을때 사용되는 Event-Broker 에는 Apache ActiveMQ, Apache Kafka, RabbitMQ 등  
여러가지가 있다. 이중에서 spring 에서 많이 쓰이는 Kafka 로 구현방법을 설명하겠다.  

