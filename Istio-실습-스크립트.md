
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

1. productpage: a python service that calls the details and reviews microservices to populate the page.
1. details: a ruby service that contains book information.
1. reviews: a java service that contains book reviews and calls the ratings microservice.
1. ratings: a node js app that contains book ranking information that accompanies a book review.

There are 3 versions of the reviews microservice:

1. version v1 doesn't call the ratings service.
1. version v2 calls the ratings service, and displays each rating as 1 to 5 black stars.
1. version v3 calls the ratings service, and displays each rating as 1 to 5 red stars.
```
istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)

#check
kubectl get services
kubectl get pods
```


