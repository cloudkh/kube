이번시간은 VueJS 를 사용하여 MSA 예제에서 사용하였던 Course 서비스를 UI 로 구성한다.  
시작전에 VueJS 를 어떻게 사용하는지 읽어보시길 바란다.  

VueJS Basics
------
* 선행학습 : [VueJS Basics#very-very-first-example](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#very-very-first-example)  

VueJS 는 java 의 class 라고 생각하면 된다.  
class 는 member field 와 operation 로 구성이 되는데, VueJS 는 여기에 face 가 추가되었다.  
**face** 는 template , 즉 위의 예제에서 `<div>`에 해당하는 영역이고,  
**member field** 는 `var app = ` 부분으로 볼 수있다.  
**operation** 은 `setInterval()` 처럼 사용한 부분이다.  

* 두번째 학습 : [VueJS Basics#incorporating-components](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#incorporating-components)  
Component 의 개념은 남이 만들어 놓은 것을 언급만으로 사용할 수 있다는 의미이다.  
생태계가 잘 구성이 되어 수많은 Vuejs 개발자들이 많은 컴포넌트들을 만들어 놓았다.  
우리는 이것을 아래처럼 선언만 하여 사용 할 수 있다.  
```hrml
<script src="https://unpkg.com/vue-material@0.7.1"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.3.3/require.js"></script>
<link rel="stylesheet" href="https://unpkg.com/vue-material@0.7.1/dist/vue-material.css">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```
아래 이미지는 sample code를 바로 html 로 만들어서 구성한 것이다.  
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/IncorporatingComponents.png)