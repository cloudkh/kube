
# MSA 개요
1. 책 내용 간추려

# 예제 애플리케이션 소개
![notes_180116_143420_4c3_1](https://user-images.githubusercontent.com/487999/34973363-9897bdea-faca-11e7-8f99-9d988bf870da.jpg)

1. 실 구현 세부 그림
![notes_180116_145341_97a_1](https://user-images.githubusercontent.com/487999/34973839-31c4bb9c-facd-11e7-8932-c5dd9c9d2749.jpg)

1. 세부 마이크로 서비스 소개
    1. 도메인 서비스들
        1. Customer Service
        1. Order Service
        1. Item Service
    1. 환경 서비스들
        1. API Gateway (Zuul)
        1. IAM
1. 컨슈머
    1. 프론트엔드
    1. BPM
    1. 3rd-party
1. IAM / Zuul

# OCE MSA 플랫폼의 사용
1. OCE 플랫폼 계정 등록 및 접속
1. 애플리케이션 생성
1. 코드 다운로드 및 빌드
1. IDE 접속
1. 서비스 접속
1. 테스트
1. 프로덕션

# 도메인 서비스의 구현
1. 주문서비스
1. 재고서비스
1. 고객서비스

# 컨슈머 만들기 1 - Web UI 만들기
1. VueJS
1. App 에 배포
1. 진입점 넣기
1. Zuul 로 진입점 통일
1. hateoas 통일 links

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

# 컨슈머 만들기 3 - 챗봇 시나리오
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
    
# Production 과 무정지 운영
1. canary
1. 모니터링과 대응
    1. 인스턴스별 
    1. 앱별
    1. 병목지점 발견
    1. 개선
1. 오토 스케일링

# 확장주제
1. 트랜잭션관리
    1. CQRS
    1. Event Sourcing
1. 서비스 보호
    1. DDOS 공격
    1. Circuit Breaker / Fallback
