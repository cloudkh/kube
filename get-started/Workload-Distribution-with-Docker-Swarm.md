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
      restart_policy:
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