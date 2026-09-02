# Spring Framework

Spring Framework와 Spring Boot에 대한 학습 내용을 정리한 공간입니다.

## Contents

### Spring 101
- [Spring 101](./101/README.md)
  - Spring Framework와 Spring Boot의 기초
  - 프로젝트 환경 설정
  - 스프링 빈과 의존성 주입
  - 스프링 MVC
  - 데이터 객체 패턴
  - 스프링 계층형 아키텍처
  - 스프링 빈 스코프와 웹 스코프
  - AOP(관점 지향 프로그래밍)
  - SOLID 원칙
  - 로깅(SLF4J, Logback)

### Spring Core
- [Spring Container](./Core/Container/README.md)
  - [Singleton Container](./Core/Container/Singleton_Container.md) - 싱글톤 패턴과 스프링 컨테이너
- [Spring Components](./Core/Component/README.md)
  - [Component Scan](./Core/Component/Component_Scan.md) - 스프링의 컴포넌트 스캔
- [Dependency Injection](./Core/Injection/README.md)
  - [Field Injection and Test Issues](./Core/Injection/FieldInjectionAndTestIssues.md) - @Autowired 필드 주입의 문제점과 테스트에서의 활용 방안

### Spring MVC
- [Spring MVC 가이드](./MVC/README.md)
  - [Spring MVC Evolution](./MVC/Spring_MVC_Evolution.md) - 스프링 MVC의 발전 역사
  - [Request Mapping](./MVC/Spring_MVC_Request_Mapping.md) - 요청 매핑
  - [Request Parameter](./MVC/Spring_MVC_Request_Parameter.md) - 요청 파라미터 처리  
  - [Filter and Interceptor](./MVC/spring_filter_and_interceptor.md) - 필터와 인터셉터
  - [Response](./MVC/Spring_MVC_Response.md) - HTTP 응답 처리

### Spring 주요 모듈
- **Spring Data**: 데이터 액세스 기술 지원
- **Spring Security**: 보안 기능 제공
- **Spring Boot**: 스프링 기반 애플리케이션의 빠른 개발 지원

## 최근 업데이트 내용
- Spring MVC 처리 과정 상세 분석
- 빈 스코프와 Provider 기능 설명
- SLF4J와 Logback을 활용한 로깅 전략
- SOLID 원칙의 실제 적용 방법

## Resources
- [Spring 공식 문서](https://spring.io/docs)
- [Spring Boot 가이드](https://spring.io/guides)
- [Spring Framework 레퍼런스](https://docs.spring.io/spring-framework/reference/) 