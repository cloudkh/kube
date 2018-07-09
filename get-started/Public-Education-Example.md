Sample Project download
------

https://github.com/uengine-oss 으로 들어가서  
https://github.com/uengine-oss/msa-tutorial-class-management-monolith 를 git clone 한다
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-monolith.git
```

해당 프로젝트를 다운받고 intellij로 열었을때, 아래와 같이 프로젝트 구조가 나온다.
현재 프로젝트는 eureka 가 있다는 가정하에 만들어진 코드이기에, application.yml 파일을 열어서 eureka.client.enabled : false 로 설정하여 준다.
```yml
eureka:
  client:
    enabled: false
``` 
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/2_1.png)

