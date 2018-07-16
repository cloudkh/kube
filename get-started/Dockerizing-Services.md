현재까지의 예제들은 local에서 port number를 하여, spring-boot:run을 하여 실행을 하였다.  
이제 한 local 머신에서 자동으로 port number를 부여하고, 사용하기 위하여  
워크로드 분산 엔진을 사용하게 된다. (workload distribution engine)  

workload distribution engine
------

![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/wokrloadDE.png)

1번 항목들은 microservice 들 이다.  
3번 항목은 실제 머신이다. 1차적으로 이 하드웨어들을 가상머신으로 포장을 하여 2번 항목으로 만들어 준다.  
그림에는 10개정도의 가상머신의 풀(VM)로 설정을 하였다. 아마존의 EC2가 그림의 3번과 2번 영역이라고 보면 된다.  
1개의 풀을 모두 사용 하면 낭비가 심하기 때문에 가상머신의 풀에 실제 마이크로 서비스들을 잘개 쪼개서 위치시킨다.
빨간색 마이크로서비스를 1개에 모두 채울수 있지만, 여러개에 분산하여 위치 시킨것을 볼 수 있다.  
각 VM안에 workload agent가 숨어져 있어서, health check를 계속 한다.  
이러한 작업들을 자동화 시킨 것이 워크로드 분산 엔진이라고 한다.  

워크로드 분산 엔진은 여러 종류가 있다.  
최근에 가장 뜨고있는 엔진은 `Kubernates` 이다. Kubernates는 태어난지 얼마 안되었지만 google에서 사용중이다.  
그리고 국내에 많은 사용자가 있는 엔진은 twiter에서 사용중인 `DC/OS` 가 있고,  
가장 할아버지 격이 `cloudfoundry` 다. 지금도 많이 쓰이고 있다.  

위 그림에서 docker가 차지하는 영역은 2번항목의 빨간색 box라고 보면 된다.  
유리벽처럼 금방금방 만들었다가, 사라지는 존재이다. 물리적인 형태는 CPU안의 하나의 Process이다.  
이것들을 잘 managing할 수 있는 것이 Docker Engine이고, Docker에서 자체적인 워크로드 분산 엔진을  
만든것이 docker Swarm 이다. Kubernates 와 경쟁을 하다가 밀린 후 현재는 교육용 목적으로 많이 쓰인다.  

docker 설치
------
여기서는 linux centos7.2 기준으로 설치를 하도록 한다.  
```
$ curl -fsSL https://get.docker.com/ | sudo sh
$ docker version
```
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/clazz-execJar.png)

```
$ curl -fsSL https://get.docker.com/ | sudo sh
$ docker version
```