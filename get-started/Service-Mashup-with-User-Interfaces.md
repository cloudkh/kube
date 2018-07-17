Service Mashup with User Interfaces
-----
Service Mashup(Service Composition) 은 여러가지 서비스를 하나로 묶어서 가치있는 무언가를 만드는 것이다.   
여러가지 방법이 있겠지만 가장 많이 사용하는 방법은 User Interfaces(UI) 를 통하여  
마이크로 서비스를 묶는 Human Interaction 한 화면을 만드는 것이다.  
최근에는 UI말고도 chatbot(대화형) 으로도 많이 Service Composition 을 하고 있다.   

예전 방식같이 서비스를 바로 UI로 만들어 내는 상황(jsp, cs program등)에서는 UI로만 나타낼수 있었기 때문에
서비스를 BPM에 묶는다던지, chatbot 에 사용하는 활용성이 떨어졌었다.  
그러나 각 서비스들을 HATEOAS 나 Restful하게 만들어 놓으면 필요에 따라서 UI 뿐만 아니라 
chatbot이나 IOT등 여러가지 인터페이스에 활용을 할 수가 있다.  

Service Mashup 필요기술
------
1. Extended Role of Front-end in MSA : Service Aggregation
1. MVVM
1. W3C Web Component Standard - Domain HTML Tags
1. Implementation : Polymer and VueJS (Another : ReactJS and Angular2)
1. Cross-Origin Resource Sharing with API Gateway ( Netflix Zuul )

#### 1. Extended Role of Front-end in MSA : Service Aggregation
아마존이나 facebook 은 겉보기에는 하나의 UI로 보이지만 
내부적으로는 천개 이상의 microservice 들의 집합이다.  
facebook 의 버튼 하나 클릭시 해당 서비스로 이동을 하여 작업이 이루어 진다.  
facebook 의 수많은 개발자 중의 하나가 된다면, 전체 화면을 구성할 필요는 없고,  
자기가 만든 서비스만 잘 구성시키면 되는 체계를 만들 수도 있다.  

#### 2. MVVM
Model-View-ViewModel 패턴. 
Excel 처럼 모델이 바뀌면 화면도 바뀐다는 개념  

#### 3. W3C Web Component Standard - Domain HTML Tags
기존에는 web page 를 하나 구성할적에 빨간색 박스 처럼 구성을 하였다.  
ABC의 구성요소가 있을적에 하나의 page 에서 해당 기능들을 묶어서 사용을 하였는데,  
소규모 화면이라면 각자 기능들의 name space 를 회피하여 구성이 가능하지만,  
서비스가 커지고 코드량이 늘어나면 name space 정하고, 구성하는데 충돌이 생길 수 있다.  
이것을 해결하고자 객체 지향 언어처럼 하나의 프로그램이지만 그 안에서 여러개의 프로그램으로 
관심사를 분리 할수있는 `class` 라고하는 interface 가 웹에도 나오게 되었다.  
이것의 표준이 W3C Web Component 라고 하고, 아래의 그림의 파란색 박스처럼 구성된다.  
 
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/w3cwebcomponents.png)

MVVM 은 도메인에서 정의한 Tags 를 하나의 HTML Tags 처럼 정의하여 사용한다.  
이전 MSA 의 예제에서 사용하였던 course 나 instructor 등의 도메인 객체들을  
<course> 형식으로 domain specific 하게 정의를 하여 사용 할 수있다.  
이렇게 정의한 Domain Tags 들이 Back-End 의 마이크로 서비스의 resource 와 맞물려  
자율적으로 상호 연계를 하여 화면에 표출이 되게 구성 할 수 있다.  

#### 4. Implementation : Polymer and VueJS
MVVM 의 실제 구현체로는 구글이 만든 Polymer, Angular2 나 
facebook이 사용하는 ReactJS, 중국에서 만든 VueJS 등이 있다.  
여기서는 VueJS를 사용하여 화면을 구성하는 예제를 만들 것이다.  

#### 5. Cross-Origin Resource Sharing
웹 브라우저에서 ajax를 통하여 여러개의 IP가 다른 마이크로 서비스를 호출할적에  
CORS(Cross-Origin Resource Sharing) 문제가 생기게 된다.  
브라우저는 1개의 도메인에서 다른 도메인으로 넘어갈적에 다른 대상과 다른 인증체계로
인식을 하기 때문에, 접근이 불가능 하다.  
이것을 해결하기 위하여 하나의 동일 도메인 처럼 인식하도록 만드는 것이 필요하다.  
이전에 설명하였듯이 Netflix Zuul을 사용한 API Gateway로 CORS문제를 해결할수있다.  

