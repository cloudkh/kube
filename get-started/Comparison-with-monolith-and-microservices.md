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
해당 코드를 모두 고쳐놓을 수 있지만, Shared Model같이 공용 jar를 사용하고, sub module로 프로젝트를 구성하는게 익숙치 않은 사용자를 위하여
trouble shooting을 하기 위하여 남겨 놓았다.

위의 프로젝트 구조에서 최상위 프로젝트는 다음과 같은 groupId와 artifactId를 가진다.
```xml
    <groupId>org.uengine.hello-class</groupId>
    <artifactId>hello-class</artifactId>
    <version>MSA-version</version>
    <packaging>pom</packaging>
```

이제 하위 module들에서는 최상위 프로젝트의 child라는 개념으로 각자의 pom파일에서 `<parent>`를 작성하여 주어야 한다.  
이렇게 `<parent>`를 적어 주어야지 최상위 프로젝트에서 mvn clean package 같은 명령으로 한꺼번에 build를 할 수 있다.
```xml
<parent>
    <groupId>org.uengine</groupId>
    <artifactId>uengine-five</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</parent>
```

그런데 여기서 최상위 pom 파일의 `<groupId>org.uengine.hello-class</groupId>` 와  
하위 pom 파일의 `<groupId>org.uengine</groupId>` 가 다르기 때문에 정상적으로 build가 안되는 문제점이 발생한다.  

이제 모든 프로젝트의 pom파일을 열어서 groupId 를 마추어 주는 작업을 해준다.
```xml
<groupId>org.uengine</groupId>
```

그리고 두번째 문제점으로, customer 프로젝트에서 shared-model 의 dependency 를 가지고 있지 않았다.  
customer 프로젝트의 pom파일을 열어서 아래와 같은 dependency를 추가하여 준다.
```xml
<dependency>
      <groupId>org.uengine</groupId>
      <artifactId>shared-model</artifactId>
      <version>1.0.0-SNAPSHOT</version>
</dependency>
```

프로젝트 구동하기
------