
# MSA 개요
1. https://www.slideshare.net/pongsor/micro-service-architecture-84941530

# 예제 애플리케이션 소개
![notes_180116_143420_4c3_1](https://user-images.githubusercontent.com/487999/34973363-9897bdea-faca-11e7-8f99-9d988bf870da.jpg)

1. 실 구현 세부 그림
![notes_180116_145341_97a_1](https://user-images.githubusercontent.com/487999/34973839-31c4bb9c-facd-11e7-8932-c5dd9c9d2749.jpg)
1. [마이크로 서비스 구현 전략](구현-전략)

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



# 컨슈머 만들기 1 - Web UI 만들기
1. [VueJS](https://github.com/TheOpenCloudEngine/micro-service-architecture-vuejs/wiki/Vue-JS-Basics)
1. [Web UI 만들기](Web-UI-만들기)
1. [IAM 연동](IAM-연동)

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
1. [챗봇 시나리오](챗봇-시나리오)
1. [카카오톡과 연동](카카오톡과-연동)
    
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
