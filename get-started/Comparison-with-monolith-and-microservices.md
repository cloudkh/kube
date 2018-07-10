Comparison with monolith and microservices
------

이번 페이지는 monolith 로 구성되어있던 [Public Education Example](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Public-Education-Example) 페이지를 microservices 로 변환을 하는 작업을 통하여 monolith와 microservices를 비교한다.  
[[Public Education Example]] 에서는 monolith 구조로 하나의 프로젝트에서 각 서비스 간의 통신을 URL링크로 relation하는 모습을 보여주었는데, microservices 구조에서는 서비스 별로 프로젝트를 나눌 것이다. 그리고, 동일 URL로 relation하는 모습을 보여주기 위하여 API GateWay와 Resistry Service가 필요하다.

https://github.com/uengine-oss 으로 들어가서  
https://github.com/uengine-oss/msa-tutorial-class-management-msa 를 git clone 한다
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-msa.git
$ cd msa-tutorial-class-management-msa
```

해당 프로젝트를 다운받고 intellij로 열었을때, 아래와 같이 프로젝트 구조가 나온다.  
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/3_1.png)

* 1개의 shared-model 프로젝트와  
* 5개의 domain  
* 2개의 general services  
로 구성이 되어있다

shared-model이 필요한 이유는 서로 다른 서비스에서 공통으로 쓰이는 객체가 있을 수 있기에 해당 객체를 공통으로 관리하여 주기 위하여 필요하다.  
예를 들면, customer가 clazz의 정보를 가져오고 싶을때 customer 서비스에서 clazz의 정보를 내부에 구현을 해놓을수도 있지만, 공용으로 관리하여 객체 모형이 바뀌는 것을 방지 할 수 있다.

프로젝트 Build하기
------
해당 프로젝트를 build하여 정상적으로 돌아가는지 확인 해 본다.

```
$ mvn clean package

[INFO] Shared Model 1.0.0-SNAPSHOT ........................ SUCCESS [  1.287 s]
[INFO] Course Sub-domain 1.0.0-SNAPSHOT ................... SUCCESS [  1.600 s]
[INFO] Class Sub-domain 1.0.0-SNAPSHOT .................... SUCCESS [  0.701 s]
[INFO] Subject Matter Experts Sub-domain 1.0.0-SNAPSHOT ... SUCCESS [  0.623 s]
[INFO] Customer Sub-domain 1.0.0-SNAPSHOT ................. FAILURE [  0.293 s]
[INFO] Shared Calendar Service 1.0.0-SNAPSHOT ............. SKIPPED
[INFO] zuul-proxy-server 0.0.1-SNAPSHOT ................... SKIPPED
[INFO] eureka-registry-server 1.0-SNAPSHOT ................ SKIPPED
[INFO] hello-class MSA-version ............................ SKIPPED

```
위와 같이 Customer 프로젝트를 build를 하는 시점에 에러가 발생한다.  
이는 Customer 서비스 안에서 Clazz를 사용하는데, 해당 클레스를 찾지 못한다는 에러이다.  
이 에러를 해결하고자 Customer 서비스의 defendency에 shared-model 을 추가하여 주자.


프로젝트 구동하기 1
------
### registry service 구동
예제 프로젝트 들은 spring-boot 위에서 기동을 한다. 그리고 microservice 를 사용하기 위하여 microservice를 잘 구현해 놓은  
spring-cloud libary를 사용하였다.  
그 중에 registry service 는 spring-cloud libary의 eureka를 사용하였고, 해당 서비스는 많은 수의 서비스들간의 앤드포인트를 찾고, 서비스의 상태를 어딘가에 등록하는 기능을 한다.
registry service를 구동하기 위하여 `mvn spring-boot:run`을 실행하고, brower의 `http://localhost:8761/` 로 접근하여 확인한다.

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/3_2.png)
아직 올라온 instance가 없는 것을 확인 할 수 있다.

### clazz, course service 구동
이제 두개의 microservice 를 구동 시킬 것이다. 역시나 프로젝트 구동은 `mvn spring-boot:run` 을 실행 할 테지만  
서로 다른 port로 구동을 시켜서 각 서비스간의 URL relation을 확인 할 것이다.  
현재는 모두 8080 으로 setting 되어있다.
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/3_3.png)

각 프로젝트의 application.yml파일을 열어서 server.port 부분을 서로 다른 포트로 설정한다.  
> mvn spring-boot:run -Dserver.port=8081 으로 구동해도 된다.
```yml
spring:
  profiles: local

server:
  port: 8081
  servletPath: /
```
* clazz 8081
* course 8082 

이제 brower의 `http://localhost:8761/` 로 접근하여 두개의 서비스가 구동 된 것을 확인 한다.

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/3_4.png)

프로젝트 구동하기 2
------
이제 [[Public Education Example]] 의 Sample project 구동하기 와 같은 작업을 해 볼 것이다.  
차이점은 이전에는 모두 같은 url인 localhost:8080 으로 호출을 하였다면, 이번에는 서로 다른 port로 호출을 할 것이다

console창을 열어서 각 서비스의 profile을 확인한다.
```
$ http http://localhost:8081/
$ http http://localhost:8082/
```


### 과정(course)등록
```
## 과정등록
$ http localhost:8082/courses title="software modeling lecture" duration=5 maxEnrollment=5 minEnrollment=1 
$ http PATCH "http://localhost:8082/courses/1" description="MSA 교욱과정입니다"

{
    "_links": {
        "course": {
            "href": "http://localhost:8082/courses/1"
        },
        "self": {
            "href": "http://localhost:8082/courses/1"
        }
    },
    "clazzes": [],
    "courseId": 1,
    "description": "MSA 교욱과정입니다",
    "duration": 5,
    "maxEnrollment": 5,
    "minEnrollment": 1,
    "title": "software modeling lecture",
    "unitPrice": null
}
```

### 강의(clazz) 등록
```
## 클래스 1 등록 
## "http://localhost:8082/courses/1" 이것이 문자열처럼 넘어가지만 HATEOAS에서는 문자열이 아니고, 리소스를 지칭하는 의미를 가진다
$ http localhost:8081/clazzes course="http://localhost:8082/courses/1"

{
    "_links": {
        "clazz": {
            "href": "http://localhost:8081/clazzes/1"
        },
        "clazzDays": {
            "href": "http://localhost:8081/clazzes/1/clazzDays"
        },
        "course": {
            "href": "http://localhost:8081/courses/1"
        },
        "instructor": {
            "href": "http://localhost:8081/instructors/"
        },
        "self": {
            "href": "http://localhost:8081/clazzes/1"
        }
    },
...
```


