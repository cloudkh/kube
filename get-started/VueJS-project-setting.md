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
그 외에 테스트 모듈을 사용할지등의 셋팅과 `npm install` 까지 할지 여부를 묻는다.  

#### 프로젝트 구조 확인 
```
$ cd vue-webpack-project
## 필요한 모듈들 install
$ npm install
```
![](https://raw.githubusercontent.com/wiki/TheOpenCloudEngine/uEngine-cloud/get-started/images/vue-webpack.png)
주요 파일들을 살펴보면  
`index.html` : 말이 필요없다시피 최초 진입점이다.  
`package.json` : npm을 설정하는 역할을 한다. `npm install` 시 package.json 파일의 dependencies, devDependencies 에 설정한 모듈명과 버전을 확인하면서 설치를 한다.  
`node_modules` : `npm install` 후 생성되는 폴더로 nodejs 모듈들을 모아놓는다. 용량이 커서 형상관리에서 제외시킨다.  
`build` : `npm build` 시 필요한 파일들을 관리한다.  
`config` : 설정파일들을 관리  
`src` : 개발 코드가 들어간다.  
`static` : static 으로 관리할 js, css 파일이나 image 파일들을 넣는 폴더  
`dist` : `npm build` 후 생성되는 폴더이다. 상용배포시 해당 폴더의 내용을 배포하면 된다.  

npm 설정을 관리하는 `package.json` 파일을 잠시 살펴보면  
```
"scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "unit": "jest --config test/unit/jest.conf.js --coverage",
    "e2e": "node test/e2e/runner.js",
    "test": "npm run unit && npm run e2e",
    "lint": "eslint --ext .js,.vue src test/unit test/e2e/specs",
    "build": "node build/build.js"
  }
```
위와같이 실헹 명령어 들을 모아 놓았다.  
로컬서버로 페이지를 실행시키려면 `npm run dev` 를 cli에 쳐도 되지만, `npm start` 라고 쳐도  
위의 scripts 를 해석하여 실행을 시켜준다.  

`devDependencies` 를 살펴보면 `"webpack": "^3.6.0"` 처럼 `^` 는 Caret Ranges 로  
[major, minor, patch] 의 minor 버전부터 major 버전까지만 허용한다는 의미이다.  
 "3.6.0" <=  "^3.6.0"  < "4.0.0"  
실제 프로젝트에서는 `"webpack": "3.6.0"` 처럼 정확한 버전을 fix해서 사용하는걸 추천한다.  
> `devDependencies` 의 모듈들중 `babel` 은 ES6 문법을 사용하기 위한 모듈이다.  

#### 프로젝트 실행
```
$ npm run dev
Your application is running here: http://localhost:8080         
```

프로젝트를 구동시키면 로컬 서버를 생성하며 http://localhost:8080 를 통하여 브라우저에서 접근이 가능하다.  
port를 변경시키려면 config/index.js 파일에서 변경을 하여 주면 된다.  