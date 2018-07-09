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

## 과정등록
```
// 과정등록
$ http localhost:8080/courses title="software modeling lecture" duration=5 maxEnrollment=5 minEnrollment=1
// 모든 정보시스템은 점차 데이터가 채워지면서 프로세스가 흘러간다.  
// 데이터를 초반에 넣지 않았더라도, PATCH를 통해 점차 데이터를 넣어준다.  
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

## Clazz등록
```
# 클래스 1 등록 - 에러 발생
http localhost:8080/clazzes course="http://localhost:8080/courses" id="1"
# "http://localhost:8080/courses/1" 이것이 문자열처럼 넘어가지만 HATEOAS에서는 문자열이 아니고, 리소스를 지칭하는 의미를 가진다
http localhost:8080/clazzes course="http://localhost:8080/courses/1"
```
