# OCE 플랫폼 계정 등록 및 접속
1. [OCE 플랫폼](https://cloud.pas-mini.io/) 에 접속하면 아래와 같은 화면에서 SIGN UP 버튼을 누른다.
![Login](https://user-images.githubusercontent.com/16382067/34975851-21bbbe26-fad7-11e7-80e4-580c726d9e51.png)

1. SIGN UP 화면에서 E-mail과, 패스워드, 이름을 입력 후 SIGN UP 버튼을 누르면 메일로 승인 확인 메일이 온다.
![Signup](https://user-images.githubusercontent.com/16382067/34975938-7aa64704-fad7-11e7-8a33-954bc72bbf4a.png)

1. 승인 확인 메일에서 가입 확인 글씨를 눌러주면 가입이 완료 된다.
![Mail](https://user-images.githubusercontent.com/16382067/34976111-54163ff8-fad8-11e7-8d6b-079b678a6719.png)

# 애플리케이션 생성
1. 로그인을 하면 아래와 같은 화면으로 넘어간다.
![Main](https://user-images.githubusercontent.com/16382067/34978941-3bdb8c4e-fae3-11e7-8c29-117a9d77dc20.png)

1. 위 그림의 작성버튼을 클릭하면 아래와 같은 화면으로 넘어간다.
![App](https://user-images.githubusercontent.com/16382067/34979768-ace15854-fae5-11e7-9d59-4be0fc2e061d.png)

1. 해당 예제에서는 Metaworks4 (Netfflix OSS)를 사용하므로 Metaworks4를 선택하면 아래와 같은 화면으로 넘어간다.
![image](https://user-images.githubusercontent.com/16382067/34980801-f44587e4-fae8-11e7-86c6-8727351f719b.png)
    1) 해당 어플리케이션의 이름을 작성해준다. 
    2) 외부접속을 위한 도메인을 작성하여준다. 
     (프로덕션주소를 작성하면, 스테이징과 개발주소는 자동으로 입력되어진다.)
    ![image](https://user-images.githubusercontent.com/16382067/34980956-670cc33c-fae9-11e7-9798-b56bf4257ee9.png)

1. 화면생성이 되면 아래와 같은 화면으로 넘어간다.
![image](https://user-images.githubusercontent.com/16382067/34981296-6f23eedc-faea-11e7-8c31-ae8ea9290a51.png)

1. 왼쪽 메뉴의 빌드 및 배포를 접속하면 아래와 같은 화면이 나온다.
![image](https://user-images.githubusercontent.com/16382067/34981374-ac8662a0-faea-11e7-96b4-54d0f4c76955.png)

1. 현재 빌드 및 배포중인 어플리케이션의 상태를 보여주며, 상태의 Running을 누르면 Gitlab으로 넘어가져서 좀 더 자세한 진행상황을 볼 수 있다.
![image](https://user-images.githubusercontent.com/16382067/34981441-d8b98bd6-faea-11e7-991f-135a8449df84.png)

# 코드 다운로드 및 빌드
1. 이전의 5번이미지에서 소스코드를 선택하면 해당 어플리케이션의 소스코드를 확인 할 수 있다.
![image](https://user-images.githubusercontent.com/16382067/34981934-63195210-faec-11e7-8321-067bb80e22cc.png)

1. 위의 스크린샷에서 빨간색 부분을 클릭하면 git의 주소가 나오고 git으로 내려받을 수 있다.
```
ex) git clone http://gitlab.pas-mini.io/sanghoon/testapp.git
```

1. 빌드는 소스코드에 오류가 없다면 커밋시, 자동으로 반영이 된다.

# IDE 접속
1. IDE를 실행 후, Import Project -> git 다운받은 경로에서 pom.xml을 선택하여 IDE로 Import 할 수 있다.

# 서비스 접속
1. 서비스 접속방법은 웹브라우저에
```
http://testapp-dev.pas-mini.io/
```
를 입력하여 접속하면,
```
{
  "_links" : {
    "customers" : {
      "href" : "http://testapp-dev.pas-mini.io/customers{?page,size,sort}",
      "templated" : true
    },
    "profile" : {
      "href" : "http://testapp-dev.pas-mini.io/profile"
    }
  }
}
```
이러한 페이지가 나오면 정상적으로 빌드가 되어 실행 된 것이다.

또는 Httpie를 이용하여 확인 할 수 있다.

Httpie가 설치되어있지 않다면 [Httpie 설치 가이드](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Httpie-%EC%84%A4%EC%B9%98)를 확인하여 설치한다.

httpie를 이용한 확인방법은 아래와 같다.

```
커맨드창에서

httpie testapp-dev.pas-mini.io를 입력하면 된다.
```
![image](https://user-images.githubusercontent.com/16382067/35023129-a9d1288c-fb7c-11e7-9030-f9ec22a04592.png)

위와 같은 메세지가 뜬다면 정상적으로 서비스가 실행 된 것이다.

# 테스트

# 프로덕션