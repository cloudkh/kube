
# MSA 개요
1. 책 내용 간추려

# 예제 애플리케이션 소개
![notes_180116_143420_4c3_1](https://user-images.githubusercontent.com/487999/34973363-9897bdea-faca-11e7-8f99-9d988bf870da.jpg)

1. 실 구현 세부 그림
![notes_180116_145341_97a_1](https://user-images.githubusercontent.com/487999/34973839-31c4bb9c-facd-11e7-8932-c5dd9c9d2749.jpg)

1. 세부 마이크로 서비스 소개
    1. 도메인 서비스들
        1. **Customer Service (고객 정보 서비스)**
           고객의 신상정보, 구매정보, 포인트 정보 등을 관리한다. 주문 서비스에 따라, 고객의 총 포인트가 갱신된다. 이후 고객의 선호 물품 정보에 따른 추천 서비스에 활용된다.
        1. **Order Service (주문 서비스)**
           고객의 물품 주문 정보를 관리한다. 주문번호에 따라 배송 추적을 할 수 있다. 주문이 이루어지면 각 제품(Item Service) 에 대한 재고량을 확인하여 주문을 받는다.
        1. **Item Service**
           제품에 대한 가격, 재고량, 포인트 정보를 관리한다. 
    1. 일반 서비스들
        1. **Semantic-Entity**  챗봇 시나리오를 위하여 입력된 자연어의 의도를 적절히 이해할 수 있도록 해주는 번역 서비스
        1. **Event Queue**  여러 마이크로 서비스들 간에 즉시적인 연동이 어려운 경우, 이를 큐에 담아 전송시키는 서비스 (Kafka를 이용)
    1. 환경 서비스들
        1. **API Gateway (Zuul)**
           API Gateway 는 각 도메인 서비스들에 대한 접근 통제 (토큰 인증), DDOS 공격등에 대한 방어, 접근 URL 단일화 등 ㄱ. 보안처리, ㄴ. 성능, ㄷ. 시스템 통합에 대한 기능을 수행하기 때문에 꼭 필요하다. 이것이 없으면 도메인 서비스들은 외부 공격에 바로 노출 될 수 있고, 프론트엔드로 접근하여 사용하는 것도 어려워 진다.   
        1. **Service Registry** 언급된 도메인 서비스들을 키값으로 등록하여 쉽게 찾을 수 있는 등록소. 이것이 있어야 서비스별로 재구동 되어도, 기존 서비스들이 새로이 올라온 서비스와 동적으로 연동될 수 있다.
        1. **IAM (통합 인증토큰 발행 서비스)**
           OAuth2.0 기반의 써드파티 인증을 포함한 여러 도메인 서비스들의 인증을 처리한다.
1. 컨슈머
    1. **프론트엔드**
       Web UI 와 모바일 UI. REST 로 노출된 도메인 서비스들을 유저들이 활용할 수 있게 한다. API Gateway 를 경유해서만 접근한다. Web UI 는 MVVM 사상을 가진 VueJS 를 기본 추천하여 개발한다.
    1. **BPM (Business Process Management)**
       BPM 을 사용하면 프로그래밍을 하지않고도 쉽게 REST 서비스들을 연동한 서비스를 만들고 변경도 용이하다. 또한 이렇게 BPM으로 개발한 프로세스 자체를 하나의 도메인 서비스로 만들 수도 있다. 기존 REST 서비스 자산들을 다양한 조합으로 재생산하여 시나리오에서 제시할 "프로모션", "배송추적", "재고조회" 등의 업무 프로세스를 구현한다.
    1. **3rd-party**
       외부 비즈니스 파트너들이 도메인 서비스를 사용할 수 있도록 한다. 
# OCE MSA 플랫폼 사용
1. [ID 등록 및 기본 기능 둘러보기](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/OCE-MSA-%ED%94%8C%EB%9E%AB%ED%8F%BC%EC%9D%98-%EC%82%AC%EC%9A%A9)
1. [관련 도구 설치](Httpie-설치)


# 도메인 서비스의 구현 (장진영 작업)
1. [주문/재고서비스](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/%EC%A3%BC%EB%AC%B8%EC%84%9C%EB%B9%84%EC%8A%A4%EC%9D%98-%EA%B5%AC%ED%98%84) 
1. [고객서비스](https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/%EA%B3%A0%EA%B0%9D%EC%84%9C%EB%B9%84%EC%8A%A4%EC%9D%98-%EA%B5%AC%ED%98%84)

# 수준 높은 마이크로 서비스
1. [마이크로 서비스의 분리](마이크로-서비스의-분리)
1. [API Gateway 통한 Host 통합과 보안 처리](API-Gateway)
1. [멀티테넌시 처리](멀티테넌시)
1. [IAM/API Gateway 을 이용한 멀티테넌시와 기능별 접근 제한](IAM-API-GW-통한-멀티테넌시와-접근제어)
1. [마이크로 서비스간 통신](마이크로서비스-간-커뮤니케이션)



# 컨슈머 만들기 1 - Web UI 만들기 (김상훈 작업)
1. VueJS
1. App 에 배포
1. 로그인 처리
1. 도메인 서비스 호출하기 (장진영)
1. Zuul 로 진입점 통일 (장진영)
1. hateoas 통일 links (장진영)

# 컨슈머 만들기 2 - BPM 통한 orchestration 
1. BPM 서비스의 디플로이
    1. Process Service
    1. Definition Service
1. BPM 서비스 접속
    1. 통합 계정 사용시
    1. 별도 계정 사용시
1. 기본 기능 테스트
    1. 프로세스 작성
    1. 프로세스 실행
    1. 프로세스 모니터링
1. 서비스 통합 프로세스의 구현
    1. 프로세스 개요
    1. 풀과 서비스 태스크
    1. SOA Maturity Model 

# 컨슈머 만들기 3 - 챗봇 시나리오 (김상훈 작업)
1. 챗봇 시나리오와 챗봇 프로세스 개요
1. 프로세스 구현
    1. Message Start Event 의 설정
    1. Send Task 를 이용한 응답 설정
    1. 조건 분기 설정
    1. 오류 발생시 Catch Error Event 설정
1. 카카오톡과의 연동
    1. 카카오 플러스 친구 등록
    1. API 대응 설정
    1. 테스트
    
# Production 과 무정지 운영 (박승필 작업)
1. canary 디플로이
1. 모니터링과 대응
    1. 인스턴스별 
    1. 앱별
    1. 병목지점 발견
    1. 개선
1. 오토 스케일링

# 확장주제 (장진영 작업)
1. 트랜잭션관리
    1. CQRS
    1. Event Sourcing
1. 서비스 보호
    1. DDOS 공격
    1. Circuit Breaker / Fallback
