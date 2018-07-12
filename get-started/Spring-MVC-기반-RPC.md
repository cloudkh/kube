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
            @PathVariable("date") String date);
            // @DateTimeFormat(pattern = "yyyy-MM-dd") @PathVariable("date") Date date);
}
```
`@RequestMapping` 을 통해 http request와 메소드를 연결할 수 있다.  
어노테이션 선언 후 method = GET/POST/DELETE 로 요청 형태를 지정할 수 있고,  
path = /(uri 주소)를 통해 이 서비스를 사용할 수 있는 상대 주소를 정의할 수 있다.  
정리를 하자면, 이 서비스는 Shared Calender 이라는 이름이며, instructorId(강사 번호)를 먼저 받고 날짜를 입력을 하면, 
그 해당 강사의 해당 날짜에 대한 결과를(스케쥴을) 리턴해주는 서비스가 이다.  

날짜를 입력할때 Date 등의 다른 객체로 정보 요청을 할 수 있지만 String(문자열) 형태로 받는 이유는,  
해당 요청을 할 경우 밑바닥에서는 Http호출과 비호출을 위한 Json 파싱과 네트워킹 작업이 이루어지는데  
이 때 주고 받는 Class 타입이 복잡해지게 되면 객체를 네트워크에 태우기 위한 Serialization 과 Deserializtion 과정에서
깨지는 경우가 발생 할 수도 있기 때문에 예제에서는 단순한 객체를 사용하였다.   
> 기타 객체를 사용해도 해결 방법이 있기에 사용여도 문제는 없다.  

여기서 리턴 타입이 `ResourceSupport`이라고 되어있는데,  
`ResourceSupport`는 HATEOAS API에서 사용하는 객체중에 하나이고,  
어떤 데이터를 온전히 다 가지고 있는 것이 아니라, 
유일한 식별자/주소(링크) 형태로 자신을 표현하는 정보인 `Resource`를 추상적으로 정의할 수 있는 클래스이다.  
여기서 List<Schedule> 등의 형태로 리턴하지 않는 이유를 설명하자면,  
해당 인터페이스를 호출하는 사람은 Schedule 객체와 같이 추가적인 dependency 없어도  
HATEOAS 수준으로 데이터를 받을 수 있기 때문이다.  
HATEOAS 에 대한 자세한 설명은 다음시간에 하도록 한다.  

### interface로 정의한 이유
> #### Proxy 패턴의 핵심  
> interface를 Define 하고 구현체를 Server-side 에 만들어 놓고,  
> Client는 이 인터페이스만 바라보고 호출을 하면, 실제 구현체가 서버에 있는지 내 로컬에 있는지 모르겠지만, 
> 구현된 결과물을 이 interface를 통해서 결과를 받을 수 있다.  

RMI(Remote Method Invocation)와 RPC(Remote procedure call) 메커니즘의 디자인 패턴은 proxy 패턴이다.  
consumer(API사용자) 입장에서는 똑같은 인터페이스를 내 로컬 객체처럼 그냥 호출해서 쓰지만,  
이 객체가 리턴하는 것은 Remote 호출 후 서버에서 만들어진 결과를 네트워크를 타고서  
내 로컬 객체처럼 뱉어주는 대리자 역할만 해주는 것이 이 인터페이스의 역할이다.  
그리고 이 인터페이스를 우리가 proxy 객체 혹은 Stub 이라고 부른다.  
이것이 interface로 정의한 이유이다.  
이렇게 Interface를 먼저 정의 해야만 Server-sided와 consumer간 합의가 된다.

### Create a Service Implementation
이제 실제구현체는 Server-sided provider(API 제공자) 쪽에 있다.  
해당 구현체를 보면, uri를 통해 2개의 변수를 받는다. (강사의 id와 조회 날짜)  
이 변수를 자체 DB(ScheduleRepository)에서 findByInstructorIdAndDate로 조회하여 돌려준다.  

#### SharedCalendarServiceImpl.java
```java
@Component
public class SharedCalendarServiceImpl implements SharedCalendarService {
    @Autowired
    ScheduleRepository scheduleRepository;
    @Override
    public ResourceSupport getSchedules(Long instructorId, String date) {
        List<Schedule> schedules = scheduleRepository.findByInstructorIdAndDate(instructorId, date);
        return new ScheduleResource(schedules);
    }
}
```
여기는 직접 DB를 통해서  조회를 하지만, 나중에 다른 Calender 서비스로 바꾸게 될 경우,  
이 implementation 객체만 변경을 하게 되면, 이 서비스를 사용하는 컨슈머들은 한번에 변경되는 효과를 얻을 수 있다.  
이러한 형식은 기존의 JAVA 클래스라고 보면 된다.  
다만 마이크로 서비스는 항상 런타임에 있다는 차이가 있을 뿐이다.  

> 담기는 구조나 소프트웨어의 dependency와 implementation에 대한 loosely coupled설계등은 
> MSA 개념에서 튀어나온 것을 아니라 기존에 갖고 있던 객체지향 OAD에서 가지고 있던 사상이다.

결론적으로  Calender Service 는 Repository에서 조회해온 데이터를  
`ScheduleResource`라는 `ResourceSupport`를 상속 받는 객체를 통해  
링크 구조가 아닌 객체를 직접 HATEOAS 수준으로 변환하여 전달한다.  
