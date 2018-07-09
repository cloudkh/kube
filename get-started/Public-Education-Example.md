Sample Project download
------

https://github.com/uengine-oss 으로 들어가서  
https://github.com/uengine-oss/msa-tutorial-class-management-monolith 를 git clone 한다
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-monolith.git
$ cd msa-tutorial-class-management-monolith
$ mvn spring-boot:run
```

해당 프로젝트를 다운받고 intellij로 열었을때, 아래와 같이 프로젝트 구조가 나온다.  
현재 프로젝트는 eureka 가 있다는 가정하에 만들어진 코드이기에, application.yml 파일을 열어서 eureka.client.enabled : false 로 설정하여 준다.
```yml
eureka:
  client:
    enabled: false
``` 
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/2_1.png)

***
이제 해당 프로젝트의 구조를 살펴보자  
HATEOAS 가 적용된 서비스는 진입점에서 조회를 하였을때 모든 설명이 다 나온다 

```
$http localhost:8080

{
    "_links": {
        "clazzDays": {
            "href": "http://localhost:8080/clazzDays{?page,size,sort}",
            "templated": true
        },
        "clazzes": {
            "href": "http://localhost:8080/clazzes{?page,size,sort}",
            "templated": true
        },
        "courses": {
            "href": "http://localhost:8080/courses{?page,size,sort}",
            "templated": true
        },
        "customers": {
            "href": "http://localhost:8080/customers{?page,size,sort}",
            "templated": true
        },
        "enrollments": {
            "href": "http://localhost:8080/enrollments{?page,size,sort}",
            "templated": true
        },
        "instructors": {
            "href": "http://localhost:8080/instructors{?page,size,sort}",
            "templated": true
        },
        "profile": {
            "href": "http://localhost:8080/profile"
        }
    }
}
```
파일 구조를 설명하자면
* Clazz 강의 
* ClazzDay 강의 날자 
* Course	교육 코스
* Customer 수강생
* Enrollment 수강생의 수강상태
* Instructor 교육 강사
위와같은 클레스 들이 있고, 해당 클래스들을 Repository로 묶어서 구성된 프로젝트이다.

Sample project 구동하기
-------

## 과정(course)등록
```
# 과정등록
$ http localhost:8080/courses title="software modeling lecture" duration=5 maxEnrollment=5 minEnrollment=1
# 모든 정보시스템은 점차 데이터가 채워지면서 프로세스가 흘러간다.  
# 데이터를 초반에 넣지 않았더라도, PATCH를 통해 점차 데이터를 넣어준다.  
$ http PATCH "http://localhost:8080/courses/1" description="MSA 교욱과정입니다"

{
    "_links": {
        "clazzes": {
            "href": "http://localhost:8080/courses/1/clazzes"
        },
        "course": {
            "href": "http://localhost:8080/courses/1"
        },
        "self": {
            "href": "http://localhost:8080/courses/1"
        }
    },
    "courseId": null,
    "description": "MSA 교욱과정입니다",
    "duration": 5,
    "maxEnrollment": 5,
    "minEnrollment": 1,
    "title": "software modeling lecture",
    "unitPrice": null
}
```

## 강의(clazz) 등록
```
# 클래스 1 등록 - 에러 발생
$ http localhost:8080/clazzes course="http://localhost:8080/courses" id="1"
# 클래스 1 등록 - 정상 작동  
# "http://localhost:8080/courses/1" 이것이 문자열처럼 넘어가지만 HATEOAS에서는 문자열이 아니고, 리소스를 지칭하는 의미를 가진다
$ http localhost:8080/clazzes course="http://localhost:8080/courses/1"

{
    "_links": {
        "clazz": {
            "href": "http://localhost:8080/clazzes/2"
        },
        "clazzDays": {
            "href": "http://localhost:8080/clazzes/2/clazzDays"
        },
        "course": {
            "href": "http://localhost:8080/clazzes/2/course"
        },
        "instructor": {
            "href": "http://localhost:8080/clazzes/2/instructor"
        },
        "self": {
            "href": "http://localhost:8080/clazzes/2"
        }
    },
    "evaluationRate": 0,
    "states": null
}
```
이 강의의 유일한 키는 2번이고, 2번 강의의 과정은 
```http "http://localhost:8080/clazzes/2/course"```
을 호출하여 확인 할 수 있다.  
http://localhost:8080/courses/1 과 같은 결과를 나타내는 것이 확인 가능하다.

> 이 두개의 API는 URL링크로 relation이 묶여 있다.  
> API 수준에서는 분리가 되어있기때문에, 나중에 서비스를 분리하더라도, API는 변화가 없기때문에 frontend에서 수정없이 사용이 가능하다.  


