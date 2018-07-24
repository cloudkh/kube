이번시간에는 Hybind 를 통하여 마이크로 서비스와 UI를 연결하는 방법을 살펴본다.  

그전에 Course.vue 와 CourseManagement.vue 의 설계 방법을 살펴보자.  
두개의 파일을 하나로 묶어도 충분히 사용이 가능하지만,  
Course.vue 파일은 완전히 외부와 단절을 시켜 놓았다.  
이 말은 Course.vue 는 외부와 통신하는 ajax 코드도 없고, 단지 화면로직만 들어가 있다.  
그리고 이것의 추가,삭제 등을 Management 에서 하도록 구현을 하여 역할분리를 확실히 하였다.  
이는 local 개발 후 prod 환경으로 넘어갈적 같은 다른 back-end 를 호출하는 경우,  
이렇게 분리를 하여 재사용도를 높일 수 있다.  

Hybind for composing User Interfaces
------
#### src/components/CourseManagement.vue#updateCourse
```javascript
updateCourse(course){
  // 1번방법 : ajax call
  $.ajax({
    url: course._links.self.href,
    method: 'PUT',
    contentType: "application/json",
    data: JSON.stringify(course),
    success:
      function(result){
       alert('Successfully Updated!');
     },
  })
  // 2번방법 : vue-resource
  this.$http.put(course._links.self.href).then(response => {
    this.someData = response.body;
    alert('Successfully Updated!')
  }, error => {
    // error callback
  });
  // 3번방법 : hybind
  course.$save().then(function(){
    alert('Successfully Updated!')
  });
}
```
위의 예제에서 http 를 호출하는 3가지 방법을 기술하였다.  
여러 모듈들을 쓰면 더욱 다양항 방법이 있지만 대표적인 방법들만 기술하였다.  
1번 ajax 방식은 jquery 를 사용한 대중적인 방법이다.  
2번 방법은 vue 에서 만든 http 모듈을 사용하여 get, post, put, delete 등을 쉽게 호출하는  
MVVM 프레임워크를 사용할때 가장 대중적인 방법이다.  

* [hybind home page](http://lbovet.github.io/hybind/)

그러나 1,2 번 방식을 사용할때는 url 을 화면단에 매핑을 해야한다는 점이 있다.  
HATEOAS API 를 사용하였을때 3번처럼 Hybind 를 사용 하여 url을 숨길 수가 있다.  
HATEOAS 에는 자기 자신의 link 와 연결될수 있는 link 정보 및 객체의 property 까지 모두 가지고다닌다.  
Hybind 에서는 다음과 같이 `window.backend = hybind("http://localhost:8080/")`  
최상위 링크만 선언을 해 놓으면 하위 링크들은 HATEOAS API 를 통하여 자동으로 찾아 매칭을 해준다.  
