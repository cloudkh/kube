JPA 에 대한 기본 설명
------
레퍼런스 참고 : http://arahansa.github.io/docs_spring/jpa.html  

이번장은 [[Gearing up the MSA]] 에서 생성하였던 코드에 JPA 부분을 좀더 자세히 설명 하고자 한다.

우선 Course 라는 Entity 클레스를 생성하여 보자

### src/Course.java
```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Course {
    @Id
    @GeneratedValue
    Long id;
    String title;
    int duration;
    String description;
    int maxEnrollment;
    int minEnrollment;
}
```