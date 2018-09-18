
# Install Istio
```
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.0.0 sh -
cd ./istio-*
export PATH=$PWD/bin:$PATH  # so that istioctl available all around the paths

# create a role for istio administration
kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole=cluster-admin \
    --user=$(gcloud config get-value core/account)

# install Custom Resource Definitions for istio 
kubectl apply -f install/kubernetes/helm/istio/templates/crds.yaml
sleep 5
kubectl apply -f install/kubernetes/istio-demo-auth.yaml
```

# Verify installation
```
$ kubectl get svc -n istio-system

NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                                                     AGE
grafana                    ClusterIP      10.98.182.192    <none>        3000/TCP                                                                                                    6m
istio-citadel              ClusterIP      10.98.13.116     <none>        8060/TCP,9093/TCP                                                                                           6m
istio-egressgateway        ClusterIP      10.104.6.171     <none>        80/TCP,443/TCP                                                                                              6m
istio-galley               ClusterIP      10.109.36.59     <none>        443/TCP,9093/TCP                                                                                            6m
istio-ingressgateway       LoadBalancer   10.110.112.66    <pending>     80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:31926/TCP,8060:31752/TCP,15030:32181/TCP,15031:31722/TCP   6m
istio-pilot                ClusterIP      10.102.61.118    <none>        15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                    
...

$ kubectl get pods -n istio-system

NAME                                        READY     STATUS      RESTARTS   AGE
grafana-66469c4d95-tj4mg                    1/1       Running     0          10m
istio-citadel-5799b76c66-5f7xz              1/1       Running     0          10m
istio-cleanup-secrets-tgqfh                 0/1       Completed   0          10m
istio-egressgateway-657f449d77-bvgzp        1/1       Running     0          10m
istio-galley-5bf4d6b8f7-m58rq               1/1       Running     0          10m
istio-grafana-post-install-m42gp            0/1       Completed   0          10m
istio-ingressgateway-b55bc6bbb-p9j2d        1/1       Running     0          10m
istio-pilot-c8ff8c54-bc6lp                  2/2       Running     0          10m
istio-policy-566866947b-z8zk7               2/2       Running     0          10m
istio-security-post-install-zr8h2           0/1       Completed   0          10m
istio-sidecar-injector-5b5fcf4df6-zg7wz     1/1       Running     0          10m
istio-statsd-prom-bridge-7f44bb5ddb-pfclt   1/1       Running     0          10m
istio-telemetry-5966685789-lxbz2            2/2       Running     0          10m
istio-tracing-ff94688bb-5lzhc               1/1       Running     0          10m
prometheus-84bd4b9796-8jh6f                 1/1       Running     0          10m
servicegraph-7875b75b4f-h7hd2               1/1       Running     0          10m
```


# Deploy Sample Application

The microservices are:

1. productpage: Python으로 개발된 제품 소개 페이지로 이하의 서비스들을 호출하여 페이지를 구성함.
1. details: 루비로 개발된 제품 detail정보 서비스
1. reviews: 자바로 개발된 Book 리뷰를 제공하고 아래의 rating서비스를 호출함
1. ratings: NodeJS로 개발된 점수 평가 기능

There are 3 versions of the reviews microservice:

1. version v1 은 rating 기능을 호출하지 않음.
1. version v2 은 rating서비스를 호출하고 별표의 색이 검정색임.
1. version v3 은 rating서비스를 호출하고 별표의 색이 붉은색임.
```
istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)

# check
kubectl get services
kubectl get pods
```

# Ingress 로 연결하기
```
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
kubectl create -f samples/bookinfo/networking/destination-rule-all-mtls.yaml
```

```
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
```

Open firewall for the service
```
export GATEWAY_URL=${INGRESS_HOST}:${INGRESS_PORT}
gcloud compute firewall-rules create allow-book --allow tcp:${INGRESS_PORT},tcp:443
```
Test the service
```
curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage   # must return 200
```
# Routing Rule의 변경

```
istioctl get destinationrules
```
will return:
```
DESTINATION-RULE NAME   HOST          SUBSETS                      NAMESPACE   AGE
details                 details       v1,v2                        default     28m
productpage             productpage   v1                           default     28m
ratings                 ratings       v1,v2,v2-mysql,v2-mysql-vm   default     28m
reviews                 reviews       v1,v2,v3                     default     28m
```

## v1 끼리만 연결하기 (rating 없음):
```
kubectl create -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```
설정 내역을 보면:
```
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
```

## v2를 'jason'이라는 유저에게만 제공하기:
```
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```
설정내역:
```
spec:
  hosts:
    - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
```
테스트 방법: Login > jason (패스워드 없음) 로그인


## Dynamic routing - route weights :
```
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
```

설정내역:
```

```