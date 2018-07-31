기본적인 Micro Service를 Springboot로 시작하는 방법.
--------

http://start.spring.io/
사이트에 들어가서 스프링 부트의 기본 프로젝트 템플릿을 생성한다.

Group과 Artifact를 입력한다. 하이픈과 소문자를 사용하여 생성하는 것이 좋다.
Dependencies 에 
* JPA	- DB ORM
* Rest Repository - 기본 Hateoas
* H2	- 기본 내장 DB

를 입력하고, Generate Project를 클릭하여 프로젝트를 생성한다.

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/1_1.png)

Artifact 에 입력한 프로젝트 명으로 zip파일이 다운로드 된다.


기본 프로젝트 구조
--------
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/1_2.png)

기본 maven프로젝트로 생성된 폴더 구조를 볼 수 있다.
여기서 mvnw 파일은 maven wrapper 로, 메이븐이 아예 없는 사람이 있기때문에 메이븐 설치없이 실행할 수 있는 파일이다.
해당 프로젝트로 폴더를 이동하여

`mvn spring-boot:run`

을 하게되면 메이븐으로 부터 library 를 다운 받은 후 기본 포트 8080으로 스프링 부트 어플리케이션이 구동 된다.  
자신이 8080포트를 사용중이고, 다른 포트로 구동을 시키고 싶다면 아래와 같이 시작하면 된다.

`mvn spring-boot:run -Dserver.port=8081`

해당 서비스 확인하기
--------

콘솔창을 하나 더 연 후에, 해당 경로로 이동을 하여 httpie 로 확인을 해볼수 있다.
([Httpie-설치](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Httpie-설치))

```
$ http localhost:8080

{
    "_links": {
        "profile": {
            "href": "http://localhost:8080/profile"
        }
    }
}
```
http localhost:8080 를 치게되면 위에 처럼 profile 정보가 나오게되는데
web으로 치면 메인 페이지로 접근을 한 것이다.
얼굴이 없다 뿐이지, 데이터만 들어가 있다.

디자인 요소만 빼고, 데이터만 남긴것이 REST api 이다.

위의 메세지는 profile이라는 sub page가 있다는 뜻이다.


파일 상세 설명
-------
#### `src/SpringBootSampleApplication.java`

```java
@SpringBootApplication
public class SpringBootSampleApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringBootSampleApplication.class, args);
	}
}
```

main Class이다. 
> **TIP: @SpringBootApplication **  
> 어노테이션으로 스프링부트 어플리케이션이라는 의미이다.  
> 요즘에는 이런식으로 java class안에서 descriptive (기술적인), declarative(선언적) 방식이 유행하고있다. - 예전에는 xml 에서 선언하였다.

#### `pom.xml`

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.0.3.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>
   ....
</dependencies>
```
parent 에서 스프링 부트가 가지고 있는 기본적인 속성들을 가져온다.  
최초 프로젝트 생성시 설정하였던 H2, JPA등이 있는것을 확인 할 수 있다.

***

이제 Person 이라는 Entity 클레스를 생성하여 보자
#### `src/Person.java`

```java
@Entity
public class Person {
    @Id
    @GeneratedValue
    Long id;
    String name;
    int age;
    String address;
}
```

Id는 바뀌면 안되는 값이기 때문에 id를 제외한 나머지 변수에 Get, Set 메서드를 만들어 준다.
> @Entity : 엔티티 클래스임을 지정하며 테이블과 매핑된다  
> @Id 만 사용하면 기본 키를 애플리케이션에서 직접 할당 하는 전략이고  
> @GeneratedValue 와 같이 사용하게 되면 자동 생성 전략이다.


***
REST 서비스를 열기 위하여 서비스 유형중에 Repository 라는 Interface를 생성하여 준다.  
이것은 DDD(Domain Driven Design) 에서 하나의 패턴중 하나인데,  
어떤 Entity에 접근하는 crud를 담고 있는 기본 기능을 가지는 서비스의 유형을 Repository 이름을 붙여서 사용한다.  

#### `src/PersonRepository.java`

```java
public interface PersonRepository extends PagingAndSortingRepository<Person, Long> {
}

```

> PagingAndSortingRepository 는 페이징 기능을 담고 있는 Repository이다  
> 레파지토리의 새로운 유형을 만들고 싶을때는 해당 Repository를 extends해서 새롭게 만들 수 있다.  
> 인터페이스의 구현체는 없지만 Repository 생성시 스프링 부트가 runtime에 실제 워킹하는 sql문을 생성하고 쿼리를 날리는 것을 만들어준다 


***
이제 프로젝트를 다시 run을 한 후에 콘솔창에서 아래와 같이 profile을 조회하여 보자
```
$ http http://localhost:8080/

{
    "_links": {
        "persons": {
            "href": "http://localhost:8080/persons{?page,size,sort}",
            "templated": true
        },
        "profile": {
            "href": "http://localhost:8080/profile"
        }
    }
}
```
Person이라는 클래스와 PersonRepository를 생성하였더니  
http://localhost:8080/profile/persons{?page,size,sort} 이라는 link가 생긴 것을 확인 할 수 있다.

```
$ http http://localhost:8080/persons page==1
```

으로 데이터를 조회하였을때 나오는 값이 없는것을 확인 가능하다.
조회시 쿼리스트링은 == 가 두게를 써야 한다.

***
이제 POST를 사용하여 데이터를 넣어보자

```
$ http POST http://localhost:8080/persons name="uengine1" address="uengine 1번째"

{
    "_links": {
        "person": {
            "href": "http://localhost:8080/persons/1"
        },
        "self": {
            "href": "http://localhost:8080/persons/1"
        }
    },
    "address": "uengine 1번째",
    "age": 0,
    "name": "uengine1"
}
```

***
넣어진 데이터를 변경 할적에는 PATCH를 쓰면 된다
```
$ http PATCH "http://localhost:8080/persons/1" age=10
{
    "_links": {
        "person": {
            "href": "http://localhost:8080/persons/1"
        },
        "self": {
            "href": "http://localhost:8080/persons/1"
        }
    },
    "address": "uengine 1번째",
    "age": 10,
    "name": "uengine1"
}
```

데이터를 많이 넣은 후 page와 sort 도 변경이 가능하다.
```
$ http "http://localhost:8080/persons/1" page==1 size==5
$ http "http://localhost:8080/persons/1" page==1 size==5 sort=="name"
$ http "http://localhost:8080/persons/1" page==1 size==5 sort=="name,asc"
```

### 이렇게 사용하는 것을 ([HATEOAS](https://spring.io/understanding/HATEOAS)) 라고 한다