VueJS 사용법을 간단히 학습한다.  
VueJS 는 우리나라에 생태계가 잘 구성되어 있고, 한글 문서로 된 guide 도 잘 되어있다.  
여기에서는 간단한 사용법을 익히고, 자세한 학습은 [guide 문서](https://kr.vuejs.org/v2/guide/index.html) 에서 참조 하기바란다.

VueJS Basics
------
* 기본 학습 : [VueJS Basics#very-very-first-example](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#very-very-first-example)  
> 여기서 사용한 예제들을 직접 html로 만들어서 작동하는것을 추천함  

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

* 세번째 학습 : [VueJS Basics#ajax-and-material](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics#ajax-and-material)  

예제 script에 보면  
`Vue.component('demo-grid',{})` 는 demo-grid 라는 custom component를 만든 것이다.  
`template` 은 얼굴이다. id가 grid-template 이라는 부분을 가져와서 자기가 어떻게 생겼다고 알려준다.  
`props` 부분이 있다. 이 부분은 java 의 public field 라고 생각하면 된다.  
외부에서 값을 셋팅하여 밀어 넣을수가 있다.  
`data` 부분은 java 로 치면 private field 이다. (내부에서만 쓸 것이다.)
```html
<demo-grid
       :data="gridData"
       :columns="gridColumns"
       :filter-key="searchQuery">
</demo-grid>
```
여기서 vuejs 를 사용할때 필수적으로 사용하는 syntax 를 간단히 설명하겠다.  
MVVM 중에 angularjs나 angular2+ 를 사용하시면 html 상에서 변수를 매핑하고,  
if 문이나 for 문을 사용하실때, ng-model, ng-if(angularjs),  
ngModel, *ngIf(angular2+) 등을 사용해 본 경험이 있으실 것이라 생각한다.  
angular 에서 `ng` 라는 prefix를 사용하였다면, vuejs에서는 `v-`를 사용한다.  
`v-bind`,`v-on`,`v-for`,`v-if`,`v-model` 등 다양하게 있는데  
이중에서 `v-bind`,`v-on` 는 Shorthands 를 사용한다.  
```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>
<!-- shorthand -->
<a :href="url"> ... </a>
```
```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>
<!-- shorthand -->
<a @click="doSomething"> ... </a>
```
위의 demo-grid 에서 `:data="gridData"` 부분은 v-bind 를 사용하여  
gridData 라는 외부 data 를 eval 하여 props 의 data 에 값을 주입(bind) 시킨 것이다.  
만약 `:` 이 없이 `data="gridData"` 라고만 사용하였다면, gridData 라고하는  
string 문자열을 data 에 넣겠다는 의미이다.  


