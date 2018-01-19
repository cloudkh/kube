
# VueJS
Vuejs와 관련된 기초는 [이곳](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics)을 참조

# App 에 배포
어플리케이션 생성시 목록에서 Vuejs를 선택하여 생성하면 된다. 
어플리케이션 생성에 관련한 내용은 [이곳](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/OCE-MSA-%ED%94%8C%EB%9E%AB%ED%8F%BC%EC%9D%98-%EC%82%AC%EC%9A%A9#%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EC%83%9D%EC%84%B1)를 참조하면 된다.
![image](https://user-images.githubusercontent.com/16382067/35032478-68651c04-fbaa-11e7-9854-c8ae93ebacde.png)

# 주문서비스 Web UI 만들기
1. 주문서비스와, 주문서비스와 연동된 Web UI를 만들기 위해서는 Metaworks4 Application과, Vuejs Application을 모두 사용 하면 된다.
주문서비스는 [링크](주문서비스의-구현)를 참조하여 만들면 된다.
주문서비스에서 필요한 것은 @id를 제외한 총 5가지이며, itemName, stock, point, price, img 이다.

1. 상품 등록페이지 Web UI 만들기
1. OrderService.vue파일을 만든다.
1. Vue파일을 생성 후에는 Router에 등록을 해주어야한다. src/router/index.js에서 OrderService.vue를 등록하여준다.
```
import OrderService from '@/components/OrderService'
Vue.component('orderservice', OrderService);
```
1. 하단의 export default new Router 부분을 보면,
<details> 
  <summary> index.js Router 수정 전 </summary>
```export default new Router
export default new Router({
//  mode: 'history',
  routes: [
    {
      path: '/',
      redirect: '/orderservice',
      name: 'home',
      component: Home,
      props: {iam: iam},
      meta: {
        breadcrumb: '홈'
      },
      children: [
        {
          path: 'orderservice',
          name: 'orderservice',
          component: orderservice,
          beforeEnter: RouterGuard.requireUser,
          meta: {
            breadcrumb: 'orderservice'
          },
        }
      ]
    },
    {
      path: '/auth/:command',
      name: 'login',
      component: Login,
      props: {iam: iam},
      beforeEnter: RouterGuard.requireGuest
    }
  ]
})
```
</details>

위와 같이 작성이 되어있다.

우리가 사용할 내용은 우선, dashboard가 아닌, orderservice이므로

<details> 
  <summary> index.js Router 수정 후 </summary>

```export default new Router
export default new Router({
//  mode: 'history',
  routes: [
    {
      path: '/',
      redirect: '/orderservice',
      name: 'home',
      component: Home,
      props: {iam: iam},
      meta: {
        breadcrumb: '홈'
      },
      children: [
        {
          path: 'orderservice',
          name: 'orderservice',
          component: orderservice,
          beforeEnter: RouterGuard.requireUser,
          meta: {
            breadcrumb: 'orderservice'
          },
        }
      ]
    },
    {
      path: '/auth/:command',
      name: 'login',
      component: Login,
      props: {iam: iam},
      beforeEnter: RouterGuard.requireGuest
    }
  ]
})
```
</details>
위와 같이 dashboard를 모두 orderservice로 바꾸어 준다.

# 도메인 서비스 호출하기 (장진영)
# Zuul 로 진입점 통일 (장진영)
# hateoas 통일 links (장진영)