이번시간은 VueJS 를 사용하여 MSA 예제에서 사용하였던 Course 서비스를 UI 로 구성한다.  

시작전에 VueJS 사용법을 간단히 학습한다.  

VueJS Basics
------
* 기본 학습 : [VueJS Basics#very-very-first-example](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#very-very-first-example)  

VueJS 는 java 의 class 라고 생각하면 된다.  
class 는 member field 와 operation 로 구성이 되는데, VueJS 는 여기에 face 가 추가되었다.  
**face** 는 template , 즉 위의 예제에서 `<div>`에 해당하는 영역이고,  
**member field** 는 `var app = ` 부분으로 볼 수있다.  
**operation** 은 `setInterval()` 처럼 사용한 부분이다.  

* 두번째 학습 : [VueJS Basics#incorporating-components](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#incorporating-components)  
Component 의 개념은 남이 만들어 놓은 것을 언급만으로 사용할 수 있다는 의미이다.  
생태계가 잘 구성이 되어 수많은 Vuejs 개발자들이 많은 컴포넌트들을 만들어 놓았다.  
우리는 이것을 아래처럼 선언만 하여 사용 할 수 있다.  
```html
<script src="https://unpkg.com/vue-material@0.7.1"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.3.3/require.js"></script>
<link rel="stylesheet" href="https://unpkg.com/vue-material@0.7.1/dist/vue-material.css">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

`<md-avatar>` , `<md-speed-dial>` 같은 custom tag 를 사용하여 언급만 하여 사용하였다.  
이것이 의미하는 바는 마이크로서비스를 만드는 팀은 사용하려는 tag 만 만들어서 관리를 하고,  
합쳐서 UI를 만드는 팀은 tag 를 사용하여 일관된 UI 구성을 할 수있는 장점이 있다.  
여기 예제에서는 vuematerial 를 사용하였지만 계속 더 좋은 component 들이 나오고 있다.  
> 참고 vuematerial site : https://vuematerial.io/getting-started  
> 참고 component 자세한 설명 : http://www.metaworks4.io/  
