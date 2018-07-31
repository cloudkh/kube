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
FK(Foreign Key)를 명시 해줘야 한다.  자동으로 생성을 해 줄수도 있지만, Course rootCourse; 같이 추가로 관계가 생성될수 있으니  
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
> spring-boot가 Hibernate를 기본으로 사용하고 있기에, 여기서는 Hibernate를 기준으로 설명한다.

### @ManyToMany
ManyToOne 과 OneToMany를 살펴 보았다.  
다대다 관계에서는 중간에 Table이 하나가 필요하다.  
[ManyToMay예제](https://vladmihalcea.com/the-best-way-to-use-the-manytomany-annotation-with-jpa-and-hibernate/) 여기서 자세한 예제를 살펴 보시길 바란다.  
잠시 설명을 드리면 Post와 Tag를 묶기 위하여 Post_tag테이블을 생성하였고,  
두 테이블의 관계를 JoinTable 과 mappedBy로 설정을 하였다. 
#### Post.java
```java
    @ManyToMany(cascade = { 
        CascadeType.PERSIST, 
        CascadeType.MERGE
    })
    @JoinTable(name = "post_tag",
        joinColumns = @JoinColumn(name = "post_id"),
        inverseJoinColumns = @JoinColumn(name = "tag_id")
    )
    private List<Tag> tags = new ArrayList<>();
```
#### Tag.java
```java
    @ManyToMany(mappedBy = "tags")
    private List<Post> posts = new ArrayList<>();
```

### 검색 조회조건 (Filltering)
데이터를 조회할적에 특정 조건으로 검색을 하는것은 당연한 요구사항이다.  
아래 설명할 방법은 JPA 방식은 아니고 Spring-data에서 쓰는 방식이다.  
Spring-data-jpa는 이 검색 조건을 naming 규칙에 의하여 사용하는 방식과 query형식으로 사용가능한 jpql방식을 사용하여 
일반적인 패턴과 복잡한 쿼리를 모두 지원한다.  
우선 아래 예제에서는 네이밍 규칙에 의한 일반적인 패턴을 조회하는 방법을 설명한다. 

#### CourseRepository.java
```java
public interface CourseRepository extends PagingAndSortingRepository<Course, Long> {
    // 1번 방법
    List<Course> findByTitle(@Param("title") String title);
    // 2번 방법
    List<Course> findByTitleContaining(@Param("title") String title);
}
```
이름에서 알 수 있듯이 1번 방법은 Title을 검색하지만, Title이 완전히 같을때 사용하는 방식이고,  
2번 방법은 Title중 일부 단어가 포함 되어있는 것을 조회할때 사용하는 방식이다.  
2번 방법이 1번 방법을 포함하고 있으니 2번 방법을 사용하면 되겠다.  

이제 어떻게 사용하는지 살펴 보겠다.  
우선 sample 데이터를 insert한다.
```
$ http localhost:8080/courses title="MSA실습" duration=5 maxEnrollment=5 minEnrollment=10
$ http localhost:8080/courses title="MSA이론" duration=5 maxEnrollment=5 minEnrollment=10
$ http localhost:8080/courses title="MSA강의" duration=5 maxEnrollment=5 minEnrollment=10
```


그 후 http localhost:8080/courses 라는 root로 조회를 하면 search라는 것이 새롭게 생겼다.  
```
$ http localhost:8080/courses

 "_links": {
        "profile": {
            "href": "http://localhost:8080/profile/courses"
        },
        "search": {
            "href": "http://localhost:8080/courses/search"
        },
        "self": {
            "href": "http://localhost:8080/courses{?page,size,sort}",
            "templated": true
        }
    },
```
HATEOAS 에서는 사용방법을 자기가 알려준다.  
```
$ http http://localhost:8080/courses/search
{
    "_links": {
        "findByTitle": {
            "href": "http://localhost:8080/courses/search/findByTitle{?title}",
            "templated": true
        },
        "findByTitleContaining": {
            "href": "http://localhost:8080/courses/search/findByTitleContaining{?title}",
            "templated": true
        },
        "self": {
            "href": "http://localhost:8080/courses/search"
        }
    }
}

## 검색 확인
$ http http://localhost:8080/courses/search/findByTitleContaining\?title="실습"
```
