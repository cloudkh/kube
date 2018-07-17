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

1. Extended Role of Front-end in MSA : Service Aggregation
1. MVVM
1. W3C Web Component Standard = Domain HTML Tags
1. Implementation : Polymer and VueJS
1. Another : ReactJS and Angular2
1. Cross-Origin Resource Sharing
1. API Gateway ( Netflix Zuul )