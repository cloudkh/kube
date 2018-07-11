JPA relation annotations and search method generation
------

이번에는 Clazz(강의) 클레스를 만들어서 Course(코스) 와의 연결고리를 JPA예제를 통해서 알아보도록 한다.  
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
    @OneToMany
    List<Clazz> clazzList;
```