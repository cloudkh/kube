Comparison with monolith and microservices
------

## 이번 페이지는 monolith 로 구성되어있던 [Public Education Example](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Public-Education-Example) 페이지를 microservices 로 변환을 하는 작업을 통하여 monolith와 microservices를 비교한다.  
### [[Public Education Example]] 에서는 monolith 구조로 하나의 프로젝트에서 각 서비스 간의 통신을 URL링크로 relation하는 모습을 보여주었는데, microservices 구조에서는 서비스 별로 프로젝트를 나눌 것이다. 그리고, 동일 URL로 relation하는 모습을 보여주기 위하여 API GateWay와 Resistry Service가 필요하다.

https://github.com/uengine-oss 으로 들어가서  
https://github.com/uengine-oss/msa-tutorial-class-management-msa 를 git clone 한다
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-msa.git
$ cd msa-tutorial-class-management-msa
```

해당 프로젝트를 다운받고 intellij로 열었을때, 아래와 같이 프로젝트 구조가 나온다.  