[[Dockerizing Services]] 에서 clazz 서비스를 Dockerizing 하는 법에 대하여 학습하였다.  
그러나 매번 이렇게 한개씩 서비스를 띄우는 방법은 mvn이나 java -jar로 실행시키는 것과 별 차이가 없다.  
이번시간에는 Docker Swarm 을 통하여 microservice들을 한번에 올리는 법에 대하여 학습한다.  

Docker Swarm을 사용하기 위하여서는 docker-compose 파일을 통하여 설정한다.  
#### msa-tutorial-class-management-msa/docker-compose.yml
```yml
services:
  class-api:
    image: clazz-service:latest
    deploy:
      replicas: 2  # 작업자(인스턴스)는 2개를 항상 유지한다
      restart_policy: # health check
        condition: on-failure
    volumes:
      - /tmp:/tmp
    ports:
      - "8088:8080"
    depends_on:
      - registry
  course-api:
    image: course-service:latest
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    volumes:
      - /tmp:/tmp
    ports:
      - "8089:8080"
    depends_on: # registry 가 뜬 후에 image를 실행한다.
      - registry
  proxy:
    image: api-gateway:latest
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
    ports:
      - "8080:8080"
    depends_on:
      - registry
  registry:
    image: uengine-registry-server:latest
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
    ports:
      - "8761:8761"
```
파일 설명을 하자면 registry 와 proxy , class-api, course-api를 띄운다.  
이전 page에서 class-api 를 dockerize하였으니 나머지 서비스들도 모두 dockerize 해준다.  
```
$ cd course/
$ sudo docker build . -t course-service
$ cd ..
$ cd proxy-service/
$ sudo docker build . -t api-gateway
$ cd ..
$ cd registry-service/
$ sudo docker build . -t uengine-registry-server
$ sudo docker images
uengine-registry-server   latest              315b6ac665a9        9 seconds ago       185MB
api-gateway               latest              5045d31821f4        33 seconds ago      184MB
course-service            latest              404c3bd9dae8        57 seconds ago      264MB
clazz-service             latest              bd09f73f2ee1        2 hours ago         264MB
```

https://github.com/uengine-oss/msa-tutorial-class-management-msa 프로젝트를 보시면  
최상위 parent 프로젝트에서 modules 로 child들을 묶어 놓고, docker-compose.yml 을 생성하였고,  
각자의 module 에 Dockerfile을 생성 하여 구성하였다.
```
$ ls
README.md  clazz   customer            pom.xml        registry-service  sme
calendar   course  docker-compose.yml  proxy-service  shared-model
```

#### 시작전에 docker swam을 실행하여야 한다.  
```
$ docker swarm init
docker swarm join --token SWMTKN-1-4mclslij7bs1n52zolzv4uqc68756bkrv5gwnmg4qg3lmk9bth-dnd0uczlrlr8dj7et1lzrbzbs 192.168.0.17:2377
```
`docker swarm init` 을 하게 되면 join 문구가 나오는데 init을 한 서버가 master가 되고, join을 통하여  
여러대의 서버를 worker 로 묶은 후 cluster로 구성을 할 수 있다.   

#### 이제 deploy 를 한다.  
```
$ sudo docker stack deploy -c docker-compose.yml getstartedlab
Creating network getstartedlab_default
Creating service getstartedlab_proxy
Creating service getstartedlab_registry
Creating service getstartedlab_class-api
Creating service getstartedlab_course-api
```
-c 옵션은 change 명령으로, docker-compose.yml 이 변경되어도 변경된 파일을 읽어서 update한다.  
replicas: 3 으로 변경하여 scale up 을 할적에 사용한다.  
제일 뒤에 getstartedlab 은 stack의 이름을 주었다.  
stack 삭제시에는 `sudo docker stack rm getstartedlab` 을 하면 된다.  
하나씩 가상의 network 를 하나씩 만들어 주고, 서비스를 하나씩 docker run을 실행 시켜준다.  

```
$ sudo docker service ls
ID                  NAME                       MODE                REPLICAS            IMAGE                            PORTS
r942gyy8dto2        getstartedlab_class-api    replicated          2/2                 clazz-service:latest             *:8088->8080/tcp
tpya4amt0nrq        getstartedlab_course-api   replicated          2/2                 course-service:latest            *:8089->8080/tcp
70408gcri6m9        getstartedlab_proxy        replicated          1/1                 api-gateway:latest               *:8080->8080/tcp
qtrs2bj6jz46        getstartedlab_registry     replicated          1/1                 uengine-registry-server:latest   *:8761->8761/tcp

$ sudo docker container ls
```
REPLICAS 로 설정한 서비스들이 각자 갯수만큼 떠있어서 `docker container ls` 를 하였을때 총 6개의 container가 돌아가고 있는것을 확인 할 수 있다.  
restart_policy 옵션에 의하여 class-api 의 한개의 서비스를 kill 하여도 서비스가 다시 살아나는것을 확인할수 있다.  

#### Self-Healing
```
$ docker ps
$ docker kill r942gyy8dto2
## 바로 실행하여 서비스가 kill 된것 확인
$ docker container ls
## 조금뒤에 서비스가 다시 살아나는것 확인
$ docker container ls
```

여기까지가 기본적인 Workload Distribution Engine의 동작 구조이다.   