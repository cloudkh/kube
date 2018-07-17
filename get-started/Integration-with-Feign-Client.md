RIntegration with Feign Client
------
여러 서비스로 나뉘어진 마이크로 서비스들이 하나의 거대한 서비스처럼 통합되는 것을 Integration 이라고 한다.

### FeignClient
여러 마이크로 서비스들이 존재를 하는 환경에서  
일일이 다른 마이크로 서비스들의 주소를 매번 찾아서 사용하는 것은 비효율적이라고 볼 수 있다.  
심지어 참조하는 마이크로 서비스의 uri 패턴이 바뀌었을 경우, 서비스 내의 모든 참조 uri를 찾아서 변환해야하는 상황이 벌어질 수 있다.  
하지만 Feign Client를 사용하게 되면 이 수고가 줄어들게 된다.  

Feign Client는 단순히 Interface로써만 존재를 하며,  
Interface에 @FeignClient(name="service_name") 어노테이션이을 추가하게 된다.  
> FeignClient는 Eureka에서 service_name이라는 이름을 가진 서비스를 찾아서 자동으로 연결을 한다.  

또한 FeignClient의 메소드들은 @RequestMapping("/uri_path") 어노테이션을 통해 메소드호출이 되면 선언된 서비스의 uri를 호출 할 수 있게 된다. 
> service_name 서비스에서 /uri_path를 호출한다는 뜻이다.  
즉, 개발자는 사용하고자 하는 API가 어디에 있던지 로컬 메소드 처럼 사용할 수 있게된다.  

이제 예제를 통하여 어떻게 사용하는지 살펴본다.  
[[Spring HATEOAS API]] 에서 `SharedCalendarServiceImpl` 을 살펴 본 것을 기억할 것이다.  
`SharedCalendarServiceImpl`은 `SharedCalendarService` 를 implements 한 것인다.  

#### SharedCalendarService.java
```java
@FeignClient("calendar")
public interface SharedCalendarService {
    @RequestMapping(
        method = {RequestMethod.GET},
        path = {"/calendar/{instructorId}/{date}"}
    )
    Resources getSchedules(@PathVariable("instructorId") Long var1, @PathVariable("date") String var2);
}
```
앞서 설명 한 내용처럼 `SharedCalendarService`는 interface 이고,  
@FeignClient("calendar") 라는 어노테이션을 붙였다.  
그리고 Impl 에서도 @RequestMapping 이 있지만 여기에서도 @RequestMapping 이 존재한다.  
> 사실 interface에서 @RequestMapping을 붙였으면 Impl에서는 @RequestMapping을 붙이면 안된다.  
> 다만 예제로 활용하기 위하여 SharedCalendarServiceImpl에도 @RequestMapping을 붙였다.  

이제 이렇게 FeignClient로 선언한 서비스를 다른 마이크로서비스에서 호출하여 보겠다.  
보통 REST 방식으로 다른 서비스를 API형식으로 call을 하여 return data로 작업하는 것이  
익숙 할수도 있지만, FeignClient 만의 장점도 있다는 것을 아래 코드를 통하여 알 수 있다.  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa/blob/master/clazz/src/main/java/hello/Clazz.java  
#### clazz/Clazz.java
```java
@Entity
public class Clazz{
   // more
   
   private void checkAvailabilityAndSetInstructor() {
        if(getInstructor()!=null){
            SharedCalendarService sharedCalendarService = 
                Application.applicationContext.getBean(SharedCalendarService.class);
            for(ClazzDay clazzDay : getClazzDays()){
                ResourceSupport schedules = 
                    sharedCalendarService.getSchedules(getInstructor().getId(), clazzDay.getDate()
                    .toString());
            // more
            }
        }
   }
}
```
`SharedCalendarService sharedCalendarService = 
                Application.applicationContext.getBean(SharedCalendarService.class);` 과 같은 방식으로  
다른 서버에 작동하고 있는 마이크로 서비스를 가져와서,  
`sharedCalendarService.getSchedules` 를 local 객체처럼 사용한 것을 볼 수 있다.   