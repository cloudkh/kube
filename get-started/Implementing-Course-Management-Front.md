이번시간은 VueJS 를 사용하여 MSA 예제에서 사용하였던 Course 서비스를 UI 로 구성한다.  
단순히 microservice 에 해당하는 UI 를 구성하는 것은 기존의 jquery와 HTML만 가지고  
충분히 구성이 가능 할 것이다. 하지만 여기서는 MVVM (Model-View-ViewModel 패턴) 을 사용하여,  
`hybind` 라는 ajax program 을 사용하여 back-end 의 객체가 곧바로 화면과 매핑이 되도록 구성을 할 것이다.  

> 이렇게 구성하였을때 나타나는 효과는 화면에서 Course 라는 back-end 객체를 loading 하여 화면에 나타내고,  
> Course 의 데이터를 지우거나 입력하는 버튼을 HTML에 만들고 호출하였을때, 별다른 코딩없이  
> 데이터 삭제나 입력이 성공하면 화면에 바로 삭제되거나 표시되도록 구성 할 수있다.  

시작하기전에 만들려는 <course> 라는 커스텀 태그의 결과물을 보자.  
```html
<course v-model="courses[index]" v-for='(course, index) in courses' 
   @change="updateCourse" 
   @remove="removeCourse" 
   @classes="jumpToClasses">
</course>
<script>
      var courses= [];
      var me = this;
      backend.$bind("courses", courses);
      courses.$load().then(function(courses){
         me.courses = courses; // set the view's data with the loaded data obtained from backend.
      });
</script>
```

마이크로 서비스와 UI 연결
------
처음부터 파일을 생성하여 만들수도 있지만, template 프로젝트를 다운받은 후 조금씩 변환하면서  
코드를 살펴보도록 하겠다.  

#### msa-tutorial-class-management-frontend 다운로드
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-frontend.git
$ cd msa-tutorial-class-management-frontend
$ npm install
$ npm run dev
> Listening at http://localhost:8082
```

브라우저를 열어서 http://localhost:8082 로 접근을 하면 iam 로그인 화면이 나온다.  
test 계정인 test1234@test.com / test1234 로 로그인을 한다.  

마이크로 서비스들과의 연결확인을 위하여 clazz, coures, zuul, eureka 서비스들을 띄운다.  
port 충돌이 안나도록 서로 다른 port로 기동한다.  
```
## 각 서비스 경로로 이동 
## clazz 서비스
$ mvn spring-boot:run -Dserver.port=8088 
## course 서비스
$ mvn spring-boot:run -Dserver.port=8081 
## zuul 서비스 8080 port
$ mvn spring-boot:run 
## eureka 서비스 8761 port
$ mvn spring-boot:run 
```

브라우저의 http://localhost:8761/ 로 접근을 하여 3개의 서비스가 정상적으로 registered 되었는지 확인한다.  
브라우저의 http://localhost:8082 접근하여 화면상의 add , update , remove 등을 통하여  
데이터를 넣고 뺼 수 있는 것을 확인 할 수 있다.  

Vue Framework Process
------
이제 template 프로젝트의 실행 구조를 파악해 보겠다.  
파일의 구조가 복잡하다고 생각되지만, 실제로 [[VueJS project setting]] 에서 생성한 프로젝트에,  
src 폴더에 약간의 코드만 넣었을뿐 나머지 구조는 똑같다.  

1. 위에서 프로젝트를 실행할적에 `npm run dev` 라는 CLI명령어를 넣었다.  
이것을 해석하자면, `package.json` 파일의 dev 라는 script를 실행시키라는 말이다.  
`package.json` 의 dev script 는 `node build/dev-server.js` 를 실행하는 명령어 이다.     

2. `build/dev-server.js` 파일을 보면 `require('./webpack.prod.conf')` 부분에서 설정파일을 가져오고,  
`webpack.prod.conf` 파일은 `webpack.base.conf` 파일을 merge 하도록 되어있다.  
`webpack.base.conf` 에서는 `./src/main.js` 를 entry 시작점으로 바라보고 있다.  

3. `./src/main.js` 파일이 해당 프로젝트의 최초 진입점이고 아래 코드 처럼
id 가 app인 element를 찾아서 Vue로 매핑을 하고 있다.  
그리고 App 이라는 components 를 찾아가라 라고 명시하고 있다.  
`import App from './App'` 처럼 쓰인 것은 App.vue 파일을 상대경로로 찾아서 App 이라는 이름으로  
사용 하겠다는 의미이다.  
```
import App from './App'
new Vue({
  el: '#app',
  template: '<App/>',
  components: {App},
```

4. 이제 `src/App.vue` 파일을 살펴보자.  template 으로 시작을 하여 component라고 명시한다.  
`<router-view></router-view>` 는 여기에 route에서 나오는 url로 매핑을 시켜  
page 들을 설정하겠다는 의미이다. route 부분은 `router/index.js` 파일에 있다.  
```
<template>
  <div>
    <router-view></router-view>
```

5. Vue를 시작할때 new Vue 형식으로 시작을 하는줄 알았지만, <script> 코드 안쪽에    
`export default` 라고 설정 부분이 있다.  
이는 default module을 생성하여 node 에 export 를 하는 것이다.  
이렇게 하였을때 해당 파일명으로 `import App from './App'` 이 사용이 가능하여 진다.  
만약 default 가 아니고 named 로 설정을 하게 된다면, 아래와 같이 사용가능하다.  
```
//------ lib.js ------
export function diag(x, y)'
//------ main.js ------
import { diag } from 'lib';
console.log(diag(4, 3));
// or
import * as lib from 'lib';
console.log(lib.diag(4, 3));
```

parent and child component 통신방법
------
이제 이번시간의 주제인 course 마이크로 서비스와 UI를 연결해 보자.  
#### src/router/index.js
```javascript
export default new Router({
  //mode: 'history',
  routes: [
    {
         children: [
             {
          path: 'courses',
          name: 'courses',
          component: CourseManagement,
          beforeEnter: RouterGuard.requireUser,
          meta: {
            breadcrumb: 'Courses'
          }
        },
````
#### src/components/CourseManagement.vue
```html
<course v-model="courses[index]" v-for='(course, index) in courses' @change="updateCourse" @remove="removeCourse" @classes="jumpToClasses"></course>

<script>
  export default {
    props: {},
    data() {},
    created() {
      var me = this;
      $.ajax(
        {
          url: 'http://localhost:8080/courses',
          success: function(result){
            me.courses = result._embedded.courses;
          }
        }
      )
    },
    watch: {},
    methods: {
        updateCourse(course){
            // do something
        }
    }
  }
</script>
``` 

1. 우선 index.js 에서 route를 설정하고 있다.  
/courses 라는 path 로 화면이 호출되었을시 CourseManagement.vue 컴포넌트를 호출하고,  
화면에 들어가기 전에 RouterGuard 에서 user 체크를 하라는 의미이다.  
meta 정보에 breadcrumb 이라는 네비게이션 컴포넌트에 Courses 라는 명칭을 넣어주었다.  

2. CourseManagement.vue 에서는 `<course>` 커스텀 컴포넌트 태그를 사용하였다.  
커스텀 컴포넌트를 만드는 방법은 아래와 같이 두가지 방법이 있지만,  
여기 예제에서는 export default 를 사용하여 Course.vue 파일을 바로 컴포넌트로 등록하였다.  
```javascript
// 1번 방법
// Vue 인스턴스 생성
new Vue({
  el: '#some-element',
  // 옵션
})
// 컴포넌트 등록
Vue.component('my-component', {
  // 옵션
})

// 2번방법
// my-component.vue 파일
<template>
   <!-- html code -->
</template>
<script>
  export default {
      // 옵션
  }
</script>
```

3. props , data() , created() , watch 등 항목은 매우 중요한 컴포넌트의 옵션들이다.  
자세한 설명은 [vue kr guide](https://kr.vuejs.org/v2/guide/components.html) 를 꼭 보길 바란다.  
예제코드에서는 jquery 로 $.ajax( 와 `url: 'http://localhost:8080/courses'` 를 사용하였는데,  
이는 매우 안좋은 코드이다.  
특히나 코드상에서 url 에 직적접으로 ip 와 port 를 매핑시키면 안된다.  
properties 파일로 url을 관리하는 방법도 좋은 방법이지만, hybind 를 사용하면 객체를 바로 매핑시킬 수 있다.  

4. `<course>` 를 호출할때 @change="updateCourse" @remove="removeCourse" 등으로 현재 메서드와 
child 에서 사용할 메서드를 v-on 시켜 놓았다.  
이 말의 의미는 child 인 Course.vue 에서 change , remove 이벤트를 발행 ($emit) 하게되면,  
parent 인 CourseManagement.vue 의 updateCourse, removeCourse 메서드가 실행된다.  

5. v-model 이라는 문구는 vue의 약속어 이다. course 에 데이터를 주입하고 싶을적에  
v-model="courses[index]" 로 전달을 하게 되면  
Course.vue 에서 `props: {value: Object}` 로 데이터를 받을 수가 있다.  
v-model 을 사용하지 않고, 직접적으로 props를 지정을 할 수가 있다.  
이때는 `<course :datas="courses[index]" >` 형식으로 데이터를 넘겨주고  
Course.vue 에서 `props: {datas: Object}` 로 데이터를 받을 수가 있다.  
> 그러나 :datas 처럼 사용을 하게 되면 화면단에서 앞으로 이와 같은 사용방법을 삭제 시킬 예정이고,  
> 원치 않은 결과가 나올수 있다고 하는 warning 이 계속 떨어진다.  