[[VueJS Basics]] 에서 기본 VueJS 를 html 파일로 만들어서 사용하는 법을 익혔다.  
그러나 실제로 프로젝트를 하기 위해서는 프레임워크 형식으로 프로젝트를 구성해야한다.  
여기서는 vue-cli를 통하여 기본환경 셋팅 및 프레임워크 구조를 살펴보겠다.  

IDE 를 사용하여 프로젝트를 생성하려면 `npm install vue` 명령으로 vue를 설치할수있다.  
NPM 은 java 로 치면 maven 같은 nodejs 의 모듈들의 집합이다.  
CLI 는 Command line interface 의 약어로 터미널을 통하여 사용자와 컴퓨터가 상호작용하는  
방식을 뜻한다.  

#### vue-cli 설치
```
$ npm install -g vue-cli
$ vue list
  ★  browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.
  ★  browserify-simple - A simple Browserify + vueify setup for quick prototyping.
  ★  pwa - PWA template for vue-cli based on the webpack template
  ★  simple - The simplest possible Vue setup in a single HTML file
  ★  webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.
  ★  webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.
```
-g 옵션은 글로벌 전역으로 모듈을 설치하라는 의미이다.  
`vue list` 를 통하여 vue 에서 제공하는 템플릿을 확인 할 수있다.  
> webpack : 프로젝트의 구조를 분석하고 자바스크립트 모듈을 비롯한 관련 리소스들을 찾은 다음 이를 브라우저에서 이용할 수 있는 번들로 묶고 패킹하는 모듈 번들러(Module bundler)다.  
> 참고 : [웹팩의 기본 개념](http://blog.jeonghwan.net/js/2017/05/15/webpack.html)
> browserify : 서버사이드 환경에서 사용되는 CommonJS 형식의 require('modules') 코드를 사용하여 의존성 관리를 할 수 있을 뿐만 아니라, 클라이언트 사이드 환경에서도 사용할 수 있도록 한데 묶어주는(Bundle) 유용한 툴이다. 즉, Node.js와 같은 형식을 웹 브라우저에서도 실행가능하게 만들어 준다.  

설명을 보면 webpack 이 browserify 를 포함하는 개념이고, 관리가 편하기 때문에 많이 쓰인다.  
이제 webpack 으로 vue project 를 생성하여 본다.

#### vue project 생성
```
## vue init <template-name> <project-name>
$ vue init webpack vue-webpack-project

? Project name vue-webpack-project
? Project description A Vue.js project
? Author kimscott <sanaloveyou@uengine.org>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner jest
? Setup e2e tests with Nightwatch? Yes
? Should we run `npm install` for you after the project has been created? (recommended) npm
```
cli 로 설치시 여러가지 옵션들이 나온다. ESLint 는 코드의 룰 설정으로 오타나 작성오류등을 파악해준다.  
그 외에 테스트 모듈을 사용할지등의 셋팅과 

npm을 설정하는 역할을 합니다. 이를 통해 모든 부품이 설치되어 돌아갈 수 있습니다