# 스프링 빈과 의존관계

이 문서는 스프링의 핵심 기능인 스프링 빈 등록과 의존관계 주입(DI)에 대해 정리한 내용입니다.  
두 가지 주요 방식, 즉 **컴포넌트 스캔과 자동 의존관계 설정** 및 **자바 코드로 직접 스프링 빈 등록하기**를 다룹니다.


## 목차
- [컴포넌트 스캔과 자동 의존관계 설정](#컴포넌트-스캔과-자동-의존관계-설정)
- [자바 코드로 직접 스프링 빈 등록하기](#자바-코드로-직접-스프링-빈-등록하기)


## 컴포넌트 스캔과 자동 의존관계 설정

### 내용 정리
- **핵심 개념:**  
  - 스프링은 `@Component` 애노테이션을 비롯한 `@Controller`, `@Service`, `@Repository` 등과 같이 특정 애노테이션이 붙은 클래스를 자동으로 스캔하여 스프링 컨테이너에 스프링 빈으로 등록합니다.
  - 이를 통해 개발자가 별도의 설정 없이도 필요한 객체를 DI(의존성 주입) 방식으로 사용할 수 있게 됩니다.
  
- **의존관계 주입 (Dependency Injection, DI):**  
  - 생성자, 필드, setter를 통해 스프링 컨테이너가 자동으로 객체 간의 의존성을 주입합니다.
  - 예를 들어, 회원 컨트롤러는 생성자에 `@Autowired`를 사용하여 회원 서비스 객체를 주입받습니다.
  
- **예시 코드:**  
  - 회원 컨트롤러의 의존관계 주입
    ```java
    package hello.hellospring.controller;

    import hello.hellospring.service.MemberService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;

    @Controller
    public class MemberController {
        private final MemberService memberService;

        @Autowired
        public MemberController(MemberService memberService) {
            this.memberService = memberService;
        }
    }
    ```
  - 회원 서비스와 회원 리포지토리 역시 각각 `@Service`와 `@Repository` 애노테이션을 통해 자동 등록됩니다.
    ```java
    package hello.hellospring.service;

    import hello.hellospring.repository.MemberRepository;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    @Service
    public class MemberService {
        private final MemberRepository memberRepository;

        @Autowired
        public MemberService(MemberRepository memberRepository) {
            this.memberRepository = memberRepository;
        }
    }
    ```
    ```java
    package hello.hellospring.repository;

    import hello.hellospring.domain.Member;
    import org.springframework.stereotype.Repository;
    import java.util.*;

    @Repository
    public class MemoryMemberRepository implements MemberRepository {
        // 구현 내용 생략
    }
    ```

- **스프링 빈 등록 특징:**  
  - 스프링은 기본적으로 빈을 **싱글톤**으로 관리하여 애플리케이션 전체에서 유일한 인스턴스를 공유합니다.
  - 컴포넌트 스캔은 특정 패키지를 대상으로 하며, 보통 `@SpringBootApplication` 애노테이션에 의해 설정된 패키지와 그 하위 패키지가 스캔 대상이 됩니다.

### 핵심 요약
- **컴포넌트 스캔:** `@Component`, `@Controller`, `@Service`, `@Repository` 등의 애노테이션을 사용하여 스프링 빈을 자동 등록.
- **자동 DI:** `@Autowired`를 통해 스프링 컨테이너가 의존 객체를 자동 주입.
- **싱글톤 관리:** 등록된 빈은 기본적으로 싱글톤으로 관리되어, 동일한 인스턴스가 공유됨.


## 자바 코드로 직접 스프링 빈 등록하기

### 내용 정리
- **핵심 개념:**  
  - 컴포넌트 스캔을 사용하지 않고, 자바 코드로 직접 스프링 빈을 설정할 수 있습니다.
  - 이 방식은 XML 설정이 아닌, **자바 기반의 설정 클래스**(`@Configuration`을 사용)를 통해 빈을 등록하는 방법입니다.
  - 이 방법은 특히, 향후 데이터 저장소나 구현 클래스를 쉽게 교체할 수 있도록 유연하게 설정할 수 있는 장점이 있습니다.

- **설정 방법:**  
  - `@Configuration` 애노테이션이 붙은 클래스 내에 `@Bean` 애노테이션을 이용하여 각 빈을 직접 등록합니다.
  - 예시 코드:
    ```java
    package hello.hellospring;

    import hello.hellospring.repository.MemberRepository;
    import hello.hellospring.repository.MemoryMemberRepository;
    import hello.hellospring.service.MemberService;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class SpringConfig {

        @Bean
        public MemberService memberService() {
            return new MemberService(memberRepository());
        }

        @Bean
        public MemberRepository memberRepository() {
            return new MemoryMemberRepository();
        }
    }
    ```
- **장점 및 활용:**  
  - 컴포넌트 스캔으로 자동 등록하기 어려운 경우나, 특정 상황에 따라 빈의 구현체를 교체해야 할 때 유용합니다.
  - XML 기반 설정보다 자바 코드 기반 설정이 가독성과 유지보수 측면에서 선호됩니다.
  - DI 방식은 필드, setter, 생성자 주입이 있지만, 일반적으로 생성자 주입을 권장합니다.

### 핵심 요약
- **자바 코드 기반 빈 등록:** `@Configuration`과 `@Bean`을 사용하여 스프링 빈을 직접 등록.
- **유연성:** 컴포넌트 스캔으로 등록하지 못하는 빈이나, 동적으로 구현 클래스를 변경해야 할 때 활용.
- **생성자 주입 권장:** DI 방식 중 생성자 주입을 통해 의존성 관리를 명시적으로 할 수 있음.

---

> **참고:**  
> - 스프링은 컴포넌트 스캔과 자바 코드 기반 설정 두 가지 방법을 모두 지원하며, 실무에서는 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔 방식을 주로 사용합니다.  
> - 상황에 따라 구현 클래스를 유연하게 변경해야 하는 경우에는 자바 코드 기반의 설정을 활용합니다.
