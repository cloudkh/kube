현재까지의 예제들은 local에서 port number를 하여, spring-boot:run을 하여 실행을 하였다.  
이제 한 local 머신에서 자동으로 port number를 부여하고, 사용하기 위하여  
워크로드 분산 엔진을 사용하게 된다. (workload distribution engine)  

workload distribution engine
------

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/wokrloadDE.png)

1번 항목들은 microservice 들 이다.  
3번 항목은 실제 머신이다. 1차적으로 이 하드웨어들을 가상머신으로 포장을 하여 2번 항목으로 만들어 준다.  
그림에는 10개정도의 가상머신의 풀로 설정을 하였다. 아마존의 EC2가 그림의 3번과 2번을 영역이라고 보면 된다.  
1개의 풀을 모두 사용 하면 낭비가 심하기 때문에 워크로드 분산엔진에 의하여 가상머신의 풀에 실제 마이크로 서비스들을 잘개 쪼개서 위치시킨다.
빨간색 마이크로서비스를 1개에 모두 채울수 있지만, 여러개에 분산하여 위치 시킨것을 볼 수 있다.  
각 VM안에 workload agent가 숨어져 있어서, health check를 계속 한다.  
이러한 작업들을 자동화 시킨 것이 워크로드 분산 엔진이라고 한다.  

