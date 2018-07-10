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
이 클레스는 `@Entity` 라고 명시를 하였다.  
import 를 할적에 javax 로 시작하는 것은 java의 표준으로 정의 되어있다는 뜻이다.  
JPA를 제공하는 provider 는 hibernate 도 있고, eclipse link 같은 것도 있다.  
이때 표준으로 제공되있는 libary를 사용하면 provider를 변경하여도 유연하게 사용이 가능하다.  

이제 getter, setter 를 생성하여 주는데, 변경하면 안되는 값인 id는 제외하고 만들어 준다.  
java 에서 getter, setter 는 경계값을 설정하여 주는 역할이다.  
member 변수들은 private 값 으로 주고 getter, setter 로 public 을 주어서 어느곳에서 변경되었는지를 파악할수 있다.  
이렇게 설정을 해야 방어적인 프로그래밍을 할 수 있다.  

### @PrePersist, @PreUpdate
```java
    @PrePersist
    @PreUpdate
    public void validation(){
        if( minEnrollment < 10 ){
            new IllegalArgumentException("수강생은 10명 이상이어 합니다.");
        }
    }
```
`@PrePersist` 어노테이션은 insert 되기 전에 해당 로직을 실행 하라는 의미이다.    
JPA framework 와 REST 는 BCI(Byte Code Instrumentation) 라는 강력한 libary를 사용하여 getter, setter 를 경유하지 않고  
데이터를 주고 받는다. 한마디로 getter에 비지니스 로직을 넣어봤자 인식이 안된다는 뜻이다.  
`@PreUpdate` 는 값이 변경될때마다 체크를 하여 해당 로직을 실행 하라는 의미이다.  
`@PreRemove` 는 값이 지워질때 체크를 한다.  
이런식으로 lifeCycle을 모두 체크할 수 있다. 

