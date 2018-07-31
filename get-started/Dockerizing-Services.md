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
최근에 가장 뜨고있는 엔진은 `Kubernetes` 이다. Kubernetes는 태어난지 얼마 안되었지만 google에서 사용중이다.  
그리고 국내에 많은 사용자가 있는 엔진은 twiter에서 사용중인 `DC/OS` 가 있고,  
가장 할아버지 격이 `cloudfoundry` 다. 지금도 많이 쓰이고 있다.  

위 그림에서 docker가 차지하는 영역은 2번항목의 빨간색 box라고 보면 된다.  
유리벽처럼 금방금방 만들었다가, 사라지는 존재이다. 물리적인 형태는 CPU안의 하나의 Process이다.  
이것들을 잘 managing할 수 있는 것이 Docker Engine이고, Docker에서 자체적인 워크로드 분산 엔진을  
만든것이 docker Swarm 이다. Kubernetes 와 경쟁을 하다가 밀린 후 현재는 교육용 목적으로 많이 쓰인다.  

#### docker 설치
여기서는 linux centos7.2 기준으로 설치를 하도록 한다.  
```
## 도커 설치
$ curl -fsSL https://get.docker.com/ | sudo sh
$ docker version
## 도커 실행
$ sudo systemctl start docker
```

Dockerizing Services
------ 
Dockerize를 한다는 것은 기존에 유지하고 있던 service를 docker 기반으로 바꾼다는 것을 의미한다.  
작은 의미에서는 단순히 docker image 를 만드는 것이고 큰 의미는 docker 를 활용해서 cluster 를 꾸미고 여기에 기존의 서비스를 빌드/배포할 체계를 만드는 것이다.

Dockerize를 시험해 보기 위하여 기존에 받았던 MSA프로젝트를 활용한다.  
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-msa.git
$ cd msa-tutorial-class-management-msa
$ cd clazz
$ mvn package -B
```
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/clazz-execJar.png)

> `mvn package` 는 파일을 compile하고 JAR같은 배포 가능한 형식으로 패키지를 하라는 명령이다.  
> '-B' 옵션은 비-대화형 모드로 mvn 실행 중 대화형 입력 모드가 나오면 기본값으로 처리하라는 의미이다.  

target에 두개의 jar파일이 생성이 되었다.  
-exec.jar 파일이 실행용 jar파일이고, 그냥 jar파일은 library용 jar파일이다.  
실행용 jar 파일은 그냥 생성이 되는것이 아닌데, 여기서는 따로 처리를 해줘서 생성이 된 것이다.  
clazz/pom.xml 을 보게되면 uengine-five 라는 parent에서 상속을 받았다.  
#### clazz/pom.xml
```xml
    <parent>
        <groupId>org.uengine</groupId>
        <artifactId>uengine-five</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>
```
#### uengine-five/pom.xml
```xml
<build>
   <plugins>
      <plugin>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-maven-plugin</artifactId>
         <configuration>
            <addResources>true</addResources>
         </configuration>
         <executions>
            <execution>
               <goals>
                  <goal>repackage</goal>
               </goals>
               <configuration>
                  <classifier>exec</classifier>
               </configuration>
            </execution>
         </executions>
      </plugin>
      <plugin>
         <groupId>com.spotify</groupId>
         <artifactId>docker-maven-plugin</artifactId>
         <version>1.0.0</version>
         <configuration>
            <imageName>${project.artifactId}</imageName>
            <dockerDirectory>.</dockerDirectory>
            <resources>
               <resource>
                  <targetPath>/</targetPath>
                  <directory>${project.build.directory}</directory>
                  <include>${project.build.finalName}.jar</include>
               </resource>
            </resources>
         </configuration>
      </plugin>
   </plugins>
</build>
```
repackage 를 사용해서 exec jar파일을 하나더 생성 하였다.  
`docker-maven-plugin` 을 사용하여 maven에서 명령 하나로 docker image까지 만드는 plugin이다.  
설정된 ${project.build.finalName}.jar 이름으로 파일을 생성하여 docker repository에 등록까지 해준다.  

이제 이 jar 파일을 실행하는 방법은 다음과 같다.  
maven 과 실행되는 것은 동일하고, 이렇게 jar파일로 만든 것은 maven의 dependency를 해제 시킨것이다.  
```
$ java -jar clazz-1.0.0-SNAPSHOT-exec.jar
```
이제 java의 dependency를 해제 시키려면 docker로 실행을 시키면 된다.  
docker로 실행을 시키기 위해서는 `docker build` 라는 명령어를 실행시켜야 하는데,  
해당 명령어에는 Dockerfile 이 필요하다. 확장자 없이 만들면 된다.  

#### clazz/Dockerfile
```
FROM openjdk:8u111-jdk-alpine
VOLUME /tmp
ADD /target/*-exec.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-Xmx256M","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```
안의 명령어를 해석하자면 FROM openjdk:8u111-jdk-alpine 위에서 실행일 할것이고,  
/tmp 라는 VOLUME을 mount할것이다. 현재폴더의 exec.jar를 app.jar로 image안에 넣으라는 뜻이다.  
EXPOSE 으로 8080 포트를 open하고, ENTRYPOINT 로 실행시 옵션을 주었다.  

파일을 생성하였으면 이제 `docker build` 를 실행한다.  
```
$ sudo docker build . -t clazz-service
Sending build context to Docker daemon  119.3MB
Step 1/5 : FROM openjdk:8u111-jdk-alpine
8u111-jdk-alpine: Pulling from library/openjdk
709515475419: Pull complete
38a1c0aaa6fd: Pull complete
5b58c996e33e: Downloading  7.073MB/49.36MB
...
Successfully built bd09f73f2ee1
Successfully tagged clazz-service:latest
```
해석하자면 현재 폴더 (.) 에서 -t(태그) 를 주어서 docker build를 하라는 의미이다.  
`docker images` 를 하여 방금 만든 image를 확인 할 수 있다.  
```
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
clazz-service       latest              bd09f73f2ee1        4 minutes ago       264MB
```

이제 만들어진 docker를 실행 시킬 차례이다.  
```
$ sudo docker run clazz-service -P 8080:8081
```
> -P 옵션은 호스트와 컨테이너의 포트를 연결(포워딩)  