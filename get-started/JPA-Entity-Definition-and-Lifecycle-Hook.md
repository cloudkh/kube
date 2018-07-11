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
JPA framework 와 REST 는 BCI(Byte Code Instrumentation) 라는 강력한 libary를 사용하여 getter, setter 를 경유하지 않고 데이터를 주고 받는다. 한마디로 getter에 비지니스 로직을 넣어봤자 인식이 안된다는 뜻이다.  
`@PreUpdate` 는 값이 변경될때마다 체크를 하여 해당 로직을 실행 하라는 의미이다.  
`@PreRemove` 는 값이 지워질때 체크를 한다.  
이런식으로 Lifecycle을 모두 체크할 수 있다. 

### @PostPersist
```
    @PostPersist
    public void greeting(){
        // 광고이메일 발송 --> 1번 방법
        // 과정 등록에 대한 이벤트 publish --> 2번 방법
        System.out.println("과정이 등록됨 " + this.getTitle());
    }
```
`@PostPersist` 어노테이션은 성공적으로 등록이 되면 그 후 일을 하겠다는 의미이다.  
1번 방법처럼 바로 비지니스 로직을 작성 할 수 있지만, 직접 여기에서 해당 microservice를 찾아서 호출하는 것 보다  
2번 방법처럼 kafka같은 message를 알려주고, 거기에 알아서 반응해 라고 만드는 방법이 훨씬 좋다.  
왜냐하면 microservice의 갯수가 많기 때문이다.  
이러한 Anotation을 통하여 Event에 반응 할수 있는 JPA가 있기에 전체 아키택쳐가 simple해 지는것을 알 수 있다.  
microservice들이 background에서 돌고 있지만, 모두 연결 될 수 있구나라는 것을 알 수 있다.  