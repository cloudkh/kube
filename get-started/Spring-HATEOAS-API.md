Spring HATEOAS API
------
#### HATEOAS는 link구조를 가져서, 자신과 연관된 다른 microservice를 연결하기 위하여 필요하다.  
이번 페이지에서는 [Spring MVC 기반 RPC](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Spring-MVC-기반-RPC) 에서 설명한 예제를 보강하여 HATEOAS API를 만드는 방법을 알아볼 것이다.  

#### SharedCalendarServiceImpl.java
```java
@RestController
public class SharedCalendarServiceImpl implements SharedCalendarService {

    @Autowired
    ScheduleRepository scheduleRepository;

    @Override
    @RequestMapping(method = RequestMethod.GET, path="/calendar/{instructorId}/{date}")
    public Resources<Resource> getSchedules(@PathVariable("instructorId") Long instructorId, @PathVariable("date") String date) {
        Date realDate = convertToDate(date);
        List<Schedule> schedules = scheduleRepository.findByInstructorId(instructorId);
        List<Resource> list  = new ArrayList<Resource>();
        for(Schedule schedule : schedules) {
            if(DateUtils.isSameDay(schedule.getDate(), realDate))
                list.add(new Resource<Schedule>(schedule));
        }
        Resources<Resource> halResources = new Resources<Resource>(list);
        //return new ScheduleResource(schedules);
        return halResources;
    }
}
```
> 참고 : https://github.com/uengine-oss/msa-tutorial-class-management-msa/blob/master/calendar/src/main/java/hello/SharedCalendarServiceImpl.java

우선 눈에 띄는 차이점은 `ResourceSupport` 로 return 을 안하고 조금 더 detail 하게,  
ArrayList로 만들어진 Resource 객체를 Resouces<Resource> 로 변환을 하여 return을 시킨다.  
* Resource : 유일한 식별자/주소(링크) 형태로 자신을 표현
* Resources : Resource 의 Collection 형태
* ResourceSupport : Resource 를 추상적으로 정의 (Resource<T> extends ResourceSupport)

Resources를 사용해야지 일반 HATEOAS API처럼 _embedded에 담겨진다.  
> Resources를 이용하여 _embedded 형태로 리턴하지 않을 경우,
> 객체가 Json형태로 넘어오거나, 다른 정보 없이 링크만 리턴할 수 있다.  
> _embedded는  HAL(Hypertext Application Language) format으로서 
> front-end에서 Hateoas 수준으로 정보가 담겨있다고 인지하여 사용하게 된다.  

여기까지하고, 실제 _embedded 방식으로 return을 하는지 확인을 해본다.  
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-msa.git
$ cd msa-tutorial-class-management-msa/calendar
## port=8085 서버시작
$ mvn spring-boot:run -Dserver.port=8085
## 강사 추가
$ http localhost:8085/schedules instructorId=1 date="2018-3-14"
## getSchedules 메서드 remote call
$ http localhost:8085/calendar/1/2018-3-14
{
    "_embedded": {
        "schedules": [
            {
                "date": 1520985600000,
                "id": 1,
                "instructorId": 1,
                "title": null
            }
        ]
    }
}
```
서버 시작시 error가 발생할 수 있지만, eureka를 안띄운 부분이니깐 현재 예제에서는 무시해도 상관없다.  
