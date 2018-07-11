JPA limitation and REST association
------
[[JPA relation annotations and search method generation]] 에서 `@ManyToOne` 과 `@OneToMany` 의 사용법을 익혔다.  
그러나 위의 방식은 Monolithic 방식에서 사용이 가능하고, spring-data-rest 방식으로 Service가 쪼개지면  
HATEOAS 는 더이상 이 Link 구조를 가지지 못하기에, HATEOAS 를 직접 써서 구현을 해야한다.  
> **TIP**
> 여기서 사용하는 예제는  
> https://github.com/uengine-oss/msa-tutorial-class-management-monolith  
> https://github.com/uengine-oss/msa-tutorial-class-management-msa  
> 여기 두개의 프로젝트에 모두 나와있다.  

다시한번 Clazz.java를 살펴보자.  
#### Clazz.java
```java
public class Clazz {
    //@RestAssociation(/*serviceId = "course", */path = "/courses/{courseId}", joinColumn = "courseId")
    @ManyToOne @JoinColumn(name="COURSE_ID")
    Course course;
}
```

