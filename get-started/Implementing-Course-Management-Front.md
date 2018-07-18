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

Implementing Course Management
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