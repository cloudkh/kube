JPA relation annotations and search method generation
------

이번에는 Clazz(강의) 클레스를 만들어서 Course(코스) 와의 연결고리를 JPA예제를 통해서 알아보도록 한다.  
### Clazz.java
```java
@Entity
@Table(name="CLASS")
public class Clazz {
    @Id
    @GeneratedValue
    Long id;

    @ManyToOne @JoinColumn(name="COURSE_ID")
    Course course;

    @Column(name = "TITLE")
    String title;
}
```
강의는 어떤 과정의 강의인지 파악하기 위하여 Course를 변수로 설정한다.  
Clazz 입장에서 보면 Course 는 ManyToOne 이다. Clazz는 여러개 인데 Course는 1개 라는 말이다.  
반대로 Course 입장에서 보면 다음과 같이 설정 할 수 있다.
### Course.java
```java
    @OneToMany(mappedBy = "course")
    List<Clazz> clazzList;
```

Course course를 `@ManyToOne`로 설정을 하였지만, 실제 DB테이블로 생각을 하였을때, 해당 테이블과 매칭을 할 수 있는 
FK를 명시 해줘야 한다.  자동으로 생성을 해 줄수도 있지만, Course rootCourse; 같이 추가로 관계가 생성될수 있으니  
`@JoinColumn(name="COURSE_ID")` 과 같이 명시적으로 Column이름을 넣어 주어야 한다.  
또한 java에서는 reserved keyword 규칙때문에 `Clazz` 라는 용어를 썼지만,  
DB에서는 `class`가 예약어가 아니기 때문에  
`@Table(name="CLASS")` 로 명명하여 class라는 테이블을 생성 할 수 있다.  
> DB용어(테이블명, 컬럼명등)는 대문자로 쓰는것이 코딩 규칙이다. 


Course.java 에서 `(mappedBy = "course")` 의 의미는  
**Clazz의 course 라고 하는 java field가 나를 ID로 물고 있다** 라는 것을 의미한다. 

> JoinColumn은 컬럼의 명칭을 주는 거고, mappedBy는 java class의 필드명
> mappedBy는 생략 할 수도 있지만 framework에 따라서 인식을 못하는 경우도 생기니  
> 명시적으로 선언을 해주는 것을 추천한다.

