JPA limitation and REST association
------
[[JPA relation annotations and search method generation]] 에서 `@ManyToOne` 과 `@OneToMany` 의 사용법을 익혔다.  
그러나 위의 방식은 Monolithic 방식에서 사용이 가능하고, spring-data-rest 방식으로 Service가 쪼개지면  
HATEOAS 는 더이상 이 Link 구조를 가지지 못하기에, HATEOAS 를 직접 써서 구현을 해야한다.  
> **TIP** : 
> 여기서 사용하는 예제는  
> https://github.com/uengine-oss/msa-tutorial-class-management-monolith  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa  
> 여기 두개의 프로젝트에 모두 나와있다.  

다시한번 Clazz.java를 살펴보자.  
#### Clazz.java
```java
public class Clazz {
    @RestAssociation(/*serviceId = "course", */path = "/courses/{courseId}", joinColumn = "courseId")
    @Transient
    //@ManyToOne @JoinColumn(name="COURSE_ID")
    Course course;
}
```
microservice로 전환시 각자의 서비스는 서로 다른 DB 를 가질수 있다. 
그리고 각 서비스는 서로 다른 process 에서 구동이 되기에, 하나의 Transient scope 안에 안들어 가진다.  
풀어 말하자면 Clazz 입장에서 Course 의 courseId 를 `@ManyToOne` 방식으로 접근이 불가능하다.  


