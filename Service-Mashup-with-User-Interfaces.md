Service Mashup with User Interfaces
-----
Service Mashup(Service Composition) 은 여러가지 서비스를 하나로 묶어서 가치있는 무언가를 만드는 것이다.   
여러가지 방법이 있겠지만 가장 많이 사용하는 방법은 User Interfaces(UI) 를 통하여  
마이크로 서비스를 묶는 Human Interaction 한 화면을 만드는 것이다.  
최근에는 UI말고도 chatbot(대화형) 으로도 많이 Service Composition 을 하고 있다.   

예전 방식같이 서비스를 바로 UI로 만들어 내는 상황(jsp, cs program등)에서는 UI로만 나타낼수 있었기 때문에
서비스를 BPM에 묶는다던지, chatbot 에 사용하는 활용성이 떨어졌었다.  
그러나 각 서비스들을 HATEOAS 나 Restful하게 만들어 놓으면 필요에 따라서 UI 뿐만 아니라 
chatbot이나 IOT등 여러가지 인터페이스 활용을 할 수가 있다.  

Service Mashup 필요기술
------
1. Extended Role of Front-end in MSA : Service Aggregation
1. MVVM
1. W3C Web Component Standard - Domain HTML Tags
1. Implementation : Polymer and VueJS
1. Another : ReactJS and Angular2
1. Cross-Origin Resource Sharing
1. API Gateway ( Netflix Zuul )

#### 1. Extended Role of Front-end in MSA : Service Aggregation
아마존이나 facebook은 겉보기에는 하나의 UI로 보이지만 내부적으로는 천개 이상의 microservice들의 집합이다.  
facebook의 버튼 하나 클릭시 해당 서비스로 이동을 하여 작업이 이루어 진다.  
facebook의 수많은 개발자 중의 하나가 된다면, 전체 화면을 구성할 필요는 없고,  
자기가 만든 서비스만 잘 구성시키면 되는 체계를 만들 수도 있다.  

#### 2. MVVM
Model-View-ViewModel패턴의 약자 이다. Excel처럼 모델이 바뀌면 화면도 바뀐다.  

#### 3. W3C Web Component Standard - Domain HTML Tags
MVVM 의 Web Component 표준을 이용하여 도메인에서 정의한 Tag들을 하나의 HTML Tag처럼 정의를 할 수 있다.  
이전 MSA의 예제에서 사용하였던 course 나 instructor 등의 도메인 객체들을  
<course> 형식으로 domain specific 하게 정의를 하여 사용 할 수있다.  