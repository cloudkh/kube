이번시간은 VueJS 를 사용하여 MSA 예제에서 사용하였던 Course 서비스를 UI 로 구성한다.  
단순히 microservice 에 해당하는 UI 를 구성하는 것은 기존의 jquery와 HTML만 가지고  
충분히 구성이 가능 할 것이다. 하지만 여기서는 MVVM (Model-View-ViewModel 패턴) 을 사용하여,  
`hybind` 라는 ajax program 을 사용하여 back-end 의 객체가 곧바로 화면과 매핑이 되도록 구성을 할 것이다.  

이렇게 구성하였을때 나타나는 효과는 화면에서 Course 라는 back-end 객체를 loading 하여 화면에 나타내고,  
Course 의 데이터를 지우거나 입력하는 버튼을 HTML에 만들고 호출하였을때, 별다른 코딩없이  
데이터 삭제나 입력이 성공하면 화면에 바로 삭제되거나 표시되도록 구성 할 수있다.  


Implementing Course Management
------
```
$ git clone https://github.com/uengine-oss/msa-tutorial-class-management-frontend.git
$ cd msa-tutorial-class-management-frontend
```