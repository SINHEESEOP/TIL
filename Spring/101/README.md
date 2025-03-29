# 🌱 Spring 101

이 섹션은 Spring Framework와 Spring Boot의 기초를 학습하는 공간입니다.

## 🎯 Why Here?
Spring Framework는 Java 기반의 엔터프라이즈 애플리케이션 개발을 위한 가장 인기 있는 프레임워크입니다. 
이 섹션에서는 Spring의 핵심 개념과 Spring Boot를 이용한 실전적인 애플리케이션 개발 방법을 학습합니다.

## 📚 Contents

### 1. 프로젝트 환경 설정
- [Spring Boot Project Setup](./spring_boot_project_setup.md)
  - 프로젝트 생성
  - 라이브러리 살펴보기
  - View 환경설정
  - 빌드하고 실행하기

### 2. Spring 기초
- [Spring Basics](./spring_basics.md)
  - 정적 컨텐츠
  - MVC와 템플릿 엔진
  - API

### 3. 스프링 빈과 의존관계
- [Spring Beans and Dependency Injection](./spring_beans_and_dependency_injection.md)
  - 컴포넌트 스캔과 자동 의존관계 설정
  - 자바 코드로 직접 스프링 빈 등록하기
- [필드 주입의 문제점과 테스트 이슈](./FieldInjection.md)
  - 필드 주입의 단점
  - 생성자 주입의 장점
  - 테스트 용이성
- [Autowired, Qualifier, Primary로 의존성 주입 관리](./AutowiredQualifierPrimary.md)
  - 여러 빈이 있을 때 해결 방법
  - @Qualifier와 @Primary 활용
  - 의존성 주입 우선순위

### 4. 스프링 데이터 객체 패턴
- [Data Objects in Spring](./data_objects_in_spring.md)
  - DTO (Data Transfer Object)
  - Entity (도메인 객체)
  - VO (Value Object)
  - DAO (Data Access Object)
- [데이터 객체 타입의 진화와 구분](./DataObjectTypes.md)
  - JDBC 시대부터 JPA/Repository까지의 흐름
  - 다양한 데이터 객체 타입 비교
  - 실무 활용 방법

### 5. 스프링 계층형 아키텍처
- [Spring Layered Architecture](./spring_layered_architecture.md)
  - 도메인(Domain)
  - 리포지토리(Repository)
  - 테스트(Test)와 "Given-When-Then" 구조
  - 서비스(Service)와 DI(Dependency Injection)

### 6. 스프링의 Model과 Entity 비교
- [Model vs Entity in Spring](./spring_model_vs_entity.md)
  - Model의 개념과 특징
  - Entity의 개념과 특징
  - Model과 Entity의 차이점
  - 실무에서의 활용 방법

### 7. 관점 지향 프로그래밍 (AOP)
- [AOP (Aspect Oriented Programming)](./spring_aop.md)
  - AOP의 개념과 필요성
  - AOP 적용 방법
  - 공통 관심사 분리의 이점
  - 실제 사용 예시

### 8. 스프링 프레임워크의 역사적 발전
- [Spring Evolution History](./spring_evolution_history.md)
  - EJB에서 Spring으로의 변화
  - Hibernate와 JPA의 등장
  - Spring Boot의 혁신
  - 현대 자바 엔터프라이즈 기술 스택

## 🛠️ Development Environment
- JDK 17 이상
- Spring Boot 3.x
- Gradle
- IntelliJ IDEA