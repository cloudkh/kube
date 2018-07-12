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

우선 눈에 띄는 차이점은 `ResourceSupport` 로 return 을 안하고,  
조금 더 detail 하게 `Resources<Resource>` 로 return을 시키는 부분이다.  
`Resources<Resource>` 는 Resource 를 List형태로 사용하고 싶을때 사용한다.  
* Resource : 유일한 식별자/주소(링크) 형태로 자신을 표현
* Resources : Resource 의 Collection 형태
* ResourceSupport : Resource를 추상적으로 정의

