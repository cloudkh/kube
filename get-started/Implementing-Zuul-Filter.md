Zuul Filter
------
Zuul 은 dynamic routing 등을 수행할 수 있는 gateway 서비스, Routing 기능뿐만 아니라,  
http 요청과 응답 사이에서 동작하는 filter를 지원함으로써 filter 구현내용에 따라  
더 다양한 기능을 추가할 수 있고 전체적으로 유연한 서비스 구성이 가능하다.  

Zuul의 ([spring-cloud-netflix reference 문서](http://cloud.spring.io/spring-cloud-netflix/multi/multi__router_and_filter_zuul.html)) 를 보면  
filter 를 총 pre, route, post 로 3가지로 설정 할 수 있게 되어있다.  
pre filter 는 request 가 들어왔을때 처음에 걸어 놓는다는 의미다.  
예제를 보면서 살펴보자.  
> https://github.com/TheOpenCloudEngine/uEngine-cloud/tree/master/uengine-cloud-zuul/src/main/java/org/uengine/zuul/pre

#### IAMFilter.java
```java
public class IAMFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "pre";
    }
    @Override
    public int filterOrder() {
        return 1;
    }
    @Override
    public boolean shouldFilter() {
        return true;
    }
    @Override
    public Object run() {
       RuleService ruleService = ApplicationContextRegistry.getApplicationContext().getBean(RuleService.class);
       // do something
    }
```
구현 내용은 제외 하였지만 ZuulFilter를 extends 하였고,  
lifeCycle 의 filterType을 "pre", filter의 Order 를 줄수 있고, filter가 동작하는 부분이 run 이다.  


RuleService 는 yml 파일이나 cloud-config 같은 설정정보를 얻어오는 서비스이다.  
filter를 만들적에 code에 직접 집어 넣을수도 있겠지만 설정파일에서 