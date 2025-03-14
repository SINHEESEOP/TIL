# 🚀 Spring 프레임워크의 역사적 발전과 자바 엔터프라이즈의 진화

EJB(Enterprise JavaBeans) 시절부터 하이버네이트(Hibernate), JPA, 그리고 **스프링(Spring)**이 탄생하고 발전해온 과정을 간략히 정리한 역사적 흐름입니다. 자바 진영이 "복잡·무거운 엔터프라이즈 애플리케이션"에서 "가볍고 유연한 프레임워크"로 옮겨가는 과정을 이해하면, 왜 현재 Spring + JPA를 많이 쓰게 되었는지 맥락을 잡을 수 있습니다.

## 📜 자바 엔터프라이즈 기술의 발전 타임라인

### 1️⃣ EJB(Enterprise JavaBeans)와 복잡했던, 무거웠던 초창기
- **EJB 2.x 시대** (1998~2000년대 초반, J2EE 시절)  
  - 대규모 기업 애플리케이션 개발을 위한 **공식 표준(스펙)** 🏢
  - XML 설정이 많고, Entity Bean(ORM 비슷한 기능)이 복잡해 "무겁다"는 불만 ⚠️
  - Bean을 컨테이너에 배포해야 해서 자유도가 낮고, 코드 테스트도 어려움 🧪


### 2️⃣ 하이버네이트(Hibernate)와 경량 프레임워크의 등장
- **Hibernate** (2001년경 공개) 🗃️ 
  - 오픈소스 **ORM**(Object-Relational Mapping) 라이브러리
  - EJB 2.x Entity Bean보다 훨씬 간단히 DB 매핑할 수 있어 큰 인기
  - DB에 접근하는 코드를 깔끔하게 작성 가능 → EJB의 복잡도를 대체
- **경량 컨테이너** 유행 🍃
  - EJB 컨테이너처럼 무겁지 않고, POJO 기반으로 간편하게 동작하는 프레임워크 선호
  - 대표적으로 **스프링 프레임워크**가 부상


### 3️⃣ 스프링(Spring) 등장 🌱
- **Rod Johnson**이 2002년 책에서 스프링의 초기 아이디어 제시
- **스프링 1.x** (2003~2004)
  - "**IoC/DI**(제어 역전·의존성 주입) + **AOP**(관점 지향)"을 제공
  - EJB 없이도 트랜잭션, 보안 등 엔터프라이즈 기능을 구현 가능 → **가볍고 유연** ✨
  - XML 설정이 EJB보다 훨씬 적고, **POJO**만 있으면 되므로 테스트가 용이
- **Hibernate와 스프링**
  - 스프링에서 Hibernate를 손쉽게 사용해 **ORM** 기능을 연결 → 복잡한 EJB를 대체


### 4️⃣ JPA(Java Persistence API) 표준화
- **EJB 3.0** (2006년, Java EE 5) 📋
  - **"Entity Bean"**이 너무 복잡하다는 비판에 따라 ORM 표준을 **JPA**로 새롭게 정의
  - **Hibernate, TopLink(EclipseLink), DataNucleus** 등 기존 ORM 라이브러리가 JPA 구현체가 됨
- **스프링 + JPA** 조합 🤝
  - 스프링의 DI/AOP + JPA(구현체로 Hibernate 등)로 엔터프라이즈 기능을 쉽게 개발
  - **Spring Data JPA**가 등장해, DB 접근 코드를 더욱 자동화


### 5️⃣ 현재: Spring Boot + JPA + Hibernate가 주류 🔝
- **Spring Boot** ⚡
  - XML 설정 거의 없이, 어노테이션 기반으로 자동 설정
  - 내장 서버, 스타터 의존성 등으로 "가볍고 빠른 개발" 지향
- **Spring Data JPA** 💾
  - JPA를 더 쉽게 사용하기 위한 스프링 하위 프로젝트
  - Repository 인터페이스만 작성하면 쿼리/CRUD 등을 자동 구현
- 결론적으로, **EJB**의 복잡함을 벗어나 "**경량 컨테이너 + POJO + JPA**" 방식이 자바 진영 표준처럼 자리잡게 됨 🏆


## 📘 자바 엔터프라이즈 용어 사전

### 1. POJO (Plain Old Java Object) 🧩
- "옛날부터 내려온 단순한 자바 오브젝트"라는 뜻
- 특별한 스펙이나 인터페이스를 구현하지 않은, **순수한 자바 클래스**
- EJB처럼 특정 규약(예: `javax.ejb` 인터페이스)을 반드시 implements하지 않아도 되는 상태를 일컫음
- 스프링이 추구하는 핵심 철학 중 하나

### 2. 하이버네이트(Hibernate) 🗄️
- 자바에서 **ORM(Object-Relational Mapping)** 기능을 제공하는 **오픈소스 라이브러리**
- 자바 객체와 RDB(관계형 데이터베이스) 테이블을 자동으로 매핑해, SQL 작성을 최소화해줌
- 현재는 **JPA** 표준 구현체 중 하나로 널리 사용됨

### 3. 컨테이너(Container) 📦
- 자바 진영에서, "스펙에 맞춰 만든 Bean(객체)을 관리하는 런타임 환경"을 부르는 말
- **EJB 컨테이너**는 EJB 객체를 관리, **서블릿 컨테이너**(톰캣 등)는 서블릿 객체를 관리
- 스프링 프레임워크도 "스프링 컨테이너"를 통해 Bean의 생성·생명주기·주입 등을 관리

### 4. J2EE/Java EE(Java 2 Platform, Enterprise Edition) 🏭
- 과거 자바 플랫폼이 웹·엔터프라이즈 기능을 묶어 표준화한 이름(지금은 "Java EE"로 명칭 변경)
- **EJB, Servlet, JSP, JMS, JTA** 등 다양한 스펙이 포함
- 과거에 "J2EE"라는 이름이었으나, 이후 "Java EE" → 현재 "Jakarta EE"로 바뀌어 옴

### 5. 엔터프라이즈(Enterprise) 🏢
- "기업용, 대규모"라는 의미
- **엔터프라이즈 애플리케이션**: 대규모 회사나 조직이 쓰는 복잡한 업무 시스템(트랜잭션, 보안, 분산처리 필요)
- EJB, Java EE 등이 엔터프라이즈 기능(분산 트랜잭션, 메시징 등)을 지원하기 위해 만들어짐


## 💡 정리 및 요약

- 초기 EJB는 기업용 기능을 공식 표준으로 제공했지만, 지나치게 복잡·무거워서 개발자들에게 외면받음
- 이에 비해 **Hibernate**(ORM), **Spring**(경량 DI/AOP 컨테이너) 등이 등장해, POJO 기반으로 쉽게 개발할 수 있는 환경을 제공하며 인기를 얻음
- 이후 EJB도 변화를 시도해 **JPA**라는 ORM 표준을 만들었고, 결국 현재는 스프링 + JPA(구현체 Hibernate) 조합이 자바 웹/엔터프라이즈 개발에서 대세가 됨

### 🔍 Spring의 발전 단계
1. Spring Framework 1.x - XML 기반 설정, DI/AOP 도입
2. Spring Framework 2.x - 애노테이션 기반 설정 도입 시작
3. Spring Framework 3.x - Java Config, REST 지원 강화
4. Spring Framework 4.x - Java 8 지원, 웹소켓 등 추가
5. Spring Boot 1.x - 자동 설정, 내장 서버 도입
6. Spring Boot 2.x - 리액티브 프로그래밍 지원
7. Spring Boot 3.x - 자바 17 지원, GraalVM 네이티브 이미지 지원

### 📚 추천 참고 자료
- [Spring 공식 문서](https://spring.io/projects/spring-framework)
- [Spring Boot 공식 문서](https://spring.io/projects/spring-boot)
- [Hibernate 공식 문서](https://hibernate.org/orm/documentation/)
- [JPA 스펙](https://jcp.org/en/jsr/detail?id=338)