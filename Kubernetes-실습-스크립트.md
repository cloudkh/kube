# 참고문서

 https://hackr.io/tutorial/introduction-to-kubernetes

# GKE 설정
```
gcloud config set compute/zone us-central1-a    # 사용 zone / region 설정
gcloud config set compute/region us-central1
gcloud container clusters create my-first-cluster   # kubernetes cluster 의 생성
gcloud compute instances list    # 인스턴스 리스트
kubectl get pods    # pod 확인
kubectl run wordpress --image=tutum/wordpress --port=80    # wordpress 서버 deploy
kubectl get pods    # wordpress 용 pod 생성을 확인
kubectl expose pod wordpress-5bb5dddcff-kwgf8 --name=wordpress --type=LoadBalancer # 위에서 생성된 wordpress pod 를 service 로 노출
kubectl get services    # 생성된 service 를 리스트내에 확인
kubectl describe service wordpress   # 생성된 service 의 세부 내용 확인, LoadBalancer Ingress:     xxx.xxx.xxx.xxx 부분 확인하여 ip 로 웹브라우저 접속하여 wordpress 가 정상 동작함을 확인

```

# 실습중 잘못 생성된 리소스 제거
```
kubectl delete services,deployment,pods --all
```

# 실습중 노드의 cpu가 꽉 차면
Pod가 deploy 되지 않고 pending이 걸리면, 주로 노드의 cpu가 꽉 찬 것이므로, GCP의 Kubernetes 클러스터 메뉴 > 클러스터 선택 > 수정 > 노드 개수 수정 (3개에서 5~6개) > 저장.
이후 조금 기다리면 자동으로 pending이 풀리고 deploy됨.

# 기본 명령 - kubectl run
```
kubectl get pods  # 실행중 pod 들을 리스팅
kubectl run first-deployment --image=nginx # command line  으로 바로 실행
kubectl get pods  # 실행된 nginx pod 들이 리스트업
(maybe one more time to show the state changed from container creation to Running)
(copy the pod name from get pods)
kubectl exec -it pod-name -- /bin/bash 
echo Hello nginx! > /usr/share/nginx/html/index.html  
apt-get update
apt-get install curl
curl localhost  # Hello nginx 가 뜬다.

```

# 도커로 자바 애플리케이션 패키징 및 실행
 https://github.com/TheOpenCloudEngine/uEngine5-base/issues/116

```
 1003  git clone https://github.com/uengine-oss/msa-tutorial-class-management-monolith.git
 1004  cd msa-tutorial-class-management-monolith/
 1006  docker build -t test .
 1007  mvn package -B
 1008  docker build -t test .
 1009  docker images
 1010  docker run test
 1011  docker build -t gcr.io/uengine-istio-test2/definition-service:v1 .
 1012  docker push gcr.io/uengine-istio-test2/definition-service:v1
 1014  kubectl run test-deploy --image=gcr.io/uengine-istio-test2/definition-service:v1
 1015  kubectl get pods
 1021  kubectl logs (생성된 pod id) -f
```

 - 주의점
1. 도커 이미지명 생성규칙
```
gcr.io/<Project ID>/<이미지 ID>:<버전>
예) gcr.io/my-project-11/definition-service:v1
```
* 프로젝트 ID는 GCP 상단의 프로젝트 선택 후, 프로젝트 목록에서 ID를 발췌복사
1. Port 넘버
Port 번호는 애플리케이션이 노출한 port 번호를 써주어야 함. e.g. python 예제는 80, 자바예제는 8080




# 설정 파일을 통한 생성 - kubectl create -f
```

kubectl get pods
nano declarative-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: declarative-pod
spec:
  containers:
  - name: memory-demo-ctr
    image: nginx

kubectl create -f declarative-pod.yaml
(copy the pod name from get pods)
kubectl exec -it memory-demo-ctr -- /bin/bash
echo Hello nginx! We are so Declarative!-- > /usr/share/nginx/html/index.html
apt-get update
apt-get install curl
curl localhost
```


# 원하는 노드 타입에 pod 몰기  e.g. GPU 에 알맞은 워크로드 몰기
```
kubectl get nodes 
kubectl label nodes <nodename> disktype=ssd
kubectl get nodes --show-labels
nano dev-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:                          #여기가 중요
    disktype: ssd

kubectl create -f dev-pod.yaml
kubectl get pods -o wide
```

# Pod initialization
```
kubectl get pods
nano init.yaml

apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  # These containers are run during pod initialization  초기 페이지를 로딩 해옴
  initContainers:
  - name: install
    image: busybox
    command:
    - wget
    - "-O"
    - "/work-dir/index.html"
    - http://kubernetes.io
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  dnsPolicy: Default
  volumes:
  - name: workdir
    emptyDir: {}

kubectl create -f init.yaml

kubectl get pods
kubectl exec -it init-demo -- /bin/bash
apt-get update
apt-get install curl
curl localhost
```


#  Liveness 와 readiness probe

```
kubectl get pods

nano exec-liveness.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5

kubectl create exec-liveness.yaml
(wait 5 sec)
kubectl describe pod liveness-exec
(wait 30 sec)
kubectl describe pod liveness-exec

nano http-liveness.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: X-Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3

kubectl create -f http-liveness.yaml
(wait 30 sec)
kubectl describe pod liveness-http

nano tcp-liveness-readiness.yaml

apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: k8s.gcr.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20

kubectl create -f tcp-liveness-readiness.yaml
(wait 20 sec)
kubectl describe pod goproxy
```

# Container lifecycle 후킹

```
kubectl get pods
nano lifecycle.yaml

apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec:
  containers:
  - name: lifecycle-demo-container
    image: nginx
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
      preStop:
        exec:
          command: ["/usr/sbin/nginx","-s","quit"]

kubectl create -f lifecycle.yaml

kubectl get pod lifecycle-demo
kubectl exec -it lifecycle-demo -- /bin/bash
cat /usr/share/message
```

# scaling
```
kubectl get pods
kubectl get deployments
(copy the name of deployment)
kubectl scale deployments (deployment name) --replicas=3
kubectl get pods
```

#  ReplicaSet
```
kubectl get pods
nano frontend.yaml

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
    matchExpressions:
      - {key: tier, operator: In, values: [frontend]}
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
        ports:
        - containerPort: 80

kubectl create -f frontend.yaml
kubectl get pods
kubectl describe rs/frontend
```

# delete ReplicaSet
```
kubectl get pods
kubectl get rs
kubectl delete pods --all
(wait a minute)
kubectl get pods
kubectl delete rs/frontend
kubectl get pods
```

# ReplicaSet 은 제거하지만 Pod 는 유지 
```
kubectl get pods
kubectl create -f frontend.yaml
kubectl delete rs/frontend --cascade=false
kubectl get pods
kubectl delete pods
```

# update labels on pod, so that new pod fired by replicaset
```
kubectl get pods
(copy the name of first pod)
kubectl describe (first pod)
KUBE_EDITOR="nano" kubectl edit pod (first pod)
kubectl describe (first pod)
kubectl get pods
kubectl describe (new pod)
```

# scale replicaset
```
kubectl get pods
kubectl get rs
nano frontend.yaml
(change number of replicas from 3 to 5)
kubectl apply -f frontend.yaml
(wait a minute)
kubectl get pods
```

# create using kubectl run
```
kubectl get pods
kubectl run example --image=nginx
kubectl get pods
kubectl describe pod (pod name)
kubectl describe deployment example
kubectl get pods
kubectl exec -it podname -- /bin/bash
echo Hello nginx! > /usr/share/nginx/html/index.html
apt-get update
apt-get install curl
curl localhost
```

# create -f
```
kubectl get pods
nano nginx-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

kubectl create -f nginx-deployment.yaml
kubectl get pods
kubectl get deployments
kubectl describe deployment nginx-deployment
kubectl get pods
(copy any of the latest pod)
kubectl exec -it podname -- /bin/bash
echo Hello nginx! > /usr/share/nginx/html/index.html
apt-get update
apt-get install curl
curl localhost
```

# Deployment 의 변경 (apply -f)
```
kubectl get pods
kubectl get deployments
nano nginx-deployment.yaml
(change number of replicas to 3)
kubectl apply -f nginx-deployment.yaml
kubectl get pods
nano nginx-deployment.yaml
(remove replica attribute)
kubectl apply -f nginx-deployment.yaml
kubectl get pods
kubectl delete pods --all
kubectl get pods
```
# rolling update
```
kubectl get pods
kubectl get deployments
kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1
kubectl rollout status deployment/nginx-deployment
kubectl get deployments
kubectl get pods
kubectl describe pods (podname)
kubectl exec -it podname -- /bin/bash
echo Hello nginx! > /usr/share/nginx/html/index.html
apt-get update
apt-get install curl
curl localhost
```

# 롤백
```
kubectl get pods
kubectl get deployments -o wide
(optional: kubectl describe deployments nginx-deployment)
kubectl rollout undo deployment/nginx-deployment
kubectl rollout history deployment/nginx-deployment
(kubectl rollout history deployment/nginx-deployment --revision=2)   #see details of the revision
(kubectl rollout undo deployment/nginx-deployment --to-revision=2)   
kubectl get deployments -o wide
(optional: kubectl describe deployments nginx-deployment)
```

# scaling

```
kubectl get pods
kubectl get deployments
kubectl scale deployments example --replicas=3
kubectl get pods
```

# 배포에 대한 pause/resume
```
kubectl get deployments -o wide
kubectl scale deployments nginx-deployment --replicas=10
kubectl rollout status deployment/nginx-deployment
kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1
kubectl rollout pause deployment/nginx-deployment
kubectl rollout status deployment/nginx-deployment
kubectl rollout resume deployment/nginx-deployment
(after a while)
kubectl rollout status deployment/nginx-deployment

```

[Tip] 다양한 배포전략 [다양한 쿠버네티스의 배포전략](Kubernetes-실습-스크립트)

# 시스템을 통한 인증 / 인가

```
  168  kubectl create sa paas
  169  kubectl describe sa paas
  170  kubectl describe secret paas-token-6p245
  171  APISERVER=$(kubectl config view | grep server | cut -f 2- -d ":" | tr -d " ")
  173  curl $APISERVER/api
  174  TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t')
  175  curl $APISERVER/api --header "Authorization: Bearer $TOKEN" --insecure
```

# Volume Mount for emptyDir
```
apiVersion: v1
kind: Pod
metadata:
  name: redis-emptydir
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage2
      mountPath: /data/redis
  volumes:
  - name: redis-storage2
    emptyDir: {}
```

# Volume Mount for PersistentVolume
```

nano pv.yml


apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo2
spec:
  storageClassName:
  capacity:
    storage: 20G
  accessModes:
    - ReadWriteOnce
  gcePersistentDisk:
    pdName: disk-2
    fsType: ext4

kubectl create -f pv.yaml

nano pvc.yaml

apiVersion: v1
kind : PersistentVolumeClaim
metadata:
  name: pv-claim-demo2
spec:
  storageClassName: ""
  volumeName: pv-demo2
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20G

kubectl create -f pvc.yml

nano redis2.yml

apiVersion: v1
kind: Pod
metadata:
  name: redis2
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage2
      mountPath: /data/redis
  volumes:
  - name: redis-storage2
    persistentVolumeClaim:
      claimName: pv-claim-demo2


```

# 멀티 티어 애플리케이션의 디플로이
예제 애플리케이션은 Mongo DB와 python frontend로 구성된 2개의 tier로 구성된다. frontend 는 mong db의 접속주소를 환경변수에서 얻어오게 프로그래밍 되어있다.
## mong db service 의 디플로이
```
nano mongodb-dep.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: rsvp-db
  labels:
    appdb: rsvpdb
spec:
  replicas: 1
  selector:
    matchLabels:
      appdb: rsvpdb
  template:
    metadata:
      labels:
        appdb: rsvpdb
    spec:
      containers:
      - name: rsvp-db
        image: mongo:3.3
        ports:
        - containerPort: 27017

kubectl create -f mongodb-dep.yml


nano mongodb-svc.yml

apiVersion: v1
kind: Service
metadata:
  name: mongodb
  labels:
    app: rsvpdb
spec:
  ports:
  - port: 27017
    protocol: TCP
  selector:
    appdb: rsvpdb

```
서비스 타입을 주지 않았으므로 기본적으로 ClusterIp type이다. 이는 외부에서 접속할 수 없어서 보안성이 높다. 주로 내부에서 사용할 db나 내부 서비스에 적용한다.

## frontend (python) 서비스의 디플로이

```
kubectl create -f mongodb-svc.yml


nano rsvp-front-dep.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: rsvp
  labels:
    app: rsvp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rsvp
  template:
    metadata:
      labels:
        app: rsvp
    spec:
      containers:
      - name: rsvp-app
        image: teamcloudyuga/rsvpapp
        env:
        - name: MONGODB_HOST
          value: mongodb
        ports:
        - containerPort: 5000
          name: web-port

kubectl create -f rsvp-front-dep.yml

```
NodeJS서비스에서 mongo DB를 접속하기 위한 소스코드는 아래와 같다:
- https://github.com/cloudyuga/rsvpapp/blob/master/rsvp.py
```
MONGODB_HOST=os.environ.get('MONGODB_HOST', 'localhost')
client = MongoClient(MONGODB_HOST, 27017)
db = client.rsvpdata

```


환경 변수인 XXX_SERVICE_HOST와 _PORT에서 얻어와서 호출함을 알 수 있다. 이 값은 환경변수가 초기에 주입되어야 한다. 이를 확인하는 방법은 아래와 같다:

```
$ kubectl exec -it rsvp-876876b6c-97lwx -- printenv | grep SERVICE
...
MONGODB_SERVICE_PORT=27017
...
```
확인되었으므로, front 서비스를 외부로 노출한다(type=LoadBalancer)
```

nano rsvp-front-svc.yml

apiVersion: v1
kind: Service
metadata:
  name: rsvp
  labels:
    app: rsvp
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: web-port
    protocol: TCP
  selector:
    app: rsvp

```

# DNS 를 통한 서비스 연동



```
git clone https://github.com/cloudyuga/rsvpapp.git
cd rsvpapp/
nano rsvp.py
(아래와 같이 편집)
MONGODB_HOST='mongodb.default.svc.cluster.local'
(저장후 exit)

docker build -t gcr.io/my-project-1531888882785/rsvp:v2 .
docker push gcr.io/my-project-1531888882785/rsvp

kubectl set image deploy/rsvp rsvp-app=gcr.io/my-project-1531888882785/rsvp:v2
```

# Secret 과 ConfigMap
```

kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2

kubectl get configmaps my-config -o yaml

nano customer1-configmap.yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.


kubectl create -f customer1-configmap.yaml

(add following fragment to the end of the env section of the deployment file - rsvp-front-dep.yml)

        - name: TEXT1
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: TEXT1
        - name: TEXT2
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: TEXT2
        - name: COMPANY
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: COMPANY


kubectl create secret generic my-password --from-literal=password=mysqlpassword

kubectl get secret my-password

kubectl describe secret my-password

echo mysqlpassword | base64

apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=



(use secret as environment variable)

         spec:
      containers:
      - image: wordpress:4.7.3-apache
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-password
              key: password


(use secret as a volume)

apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret

```

# Autoscale
```
kubectl autoscale deploy def2 --min=2 --max=10 --cpu-percent=50
kubectl get hpa   # hpa = HorizontalPodAutoscaler
kubectl edit hpa def2
```



# 2-Tier MSA Application Deploy
```
1005  git clone https://github.com/cloudyuga/rsvpapp.git
 1006  cd rsvpapp/
 1007  ls
 1008  nano Dockerfile
 1011  docker build -t gcr.io/uengine-istio-test/rsvp:v1 .
 1012  docker push  gcr.io/uengine-istio-test/rsvp:v1
 1013  kubectl run rsvp --image=gcr.io/uengine-istio-test/rsvp:v1
 1014  kubectl get po -l run=rsvp
 1016  kubectl expose deploy/rsvp --port=5000 --type=LoadBalancer
 1017  kubectl get svc rsvp -w
 1020  kubectl run mongodb --image=mongo:3.3
 1025  kubectl expose deploy/mongodb --port=27017 --protocol=TCP
 1026  kubectl get svc/mongodb
 1027  kubectl exec -it rsvp-749f8795d6-5g527 -- /bin/sh
      printenv
      exit
 1028  kubectl delete po
 1029  kubectl delete po  rsvp-749f8795d6-5g527
 1030  kubectl get po -l run=rsvp
 1031  kubectl exec -it rsvp-749f8795d6-c962b -- /bin/sh
      printenv | grep MONGO
      exit
 1032  nano rsvp.py
      MONGODB_HOST --> MONGODB_SERVICE_HOST 로 변경
      (write & out)
 1033  docker build -t gcr.io/uengine-istio-test/rsvp:v2 .
 1034  docker push gcr.io/uengine-istio-test/rsvp:v2
 1035  kubectl set image deploy/rsvp rsvp=gcr.io/uengine-istio-test/rsvp:v2
```

[Next: Istio 실습](Istio-실습-스크립트)