# Summary

# Table of contents

* [TodayILearned](./README.md) - 오늘 배운 것

## CS (Computer Science) 💻

* [DataStructure](./CS/DataStructure/README.md) - 데이터 구조
  * [자료구조 개념서](./CS/DataStructure/00_Core_Concept/자료구조_개념서.md) - 자료구조의 근본과 이해 (4단계 정의법)
  * **01_Physical_Basis** (물리적 기반)
    * [Physical Basis](./CS/DataStructure/01_Physical_Basis/README.md) - 배열/연결 기반 물리 구조
  * **02_ADT** (추상 자료형)
    * [Map](./CS/DataStructure/02_ADT/Map.md) - 맵(Map)의 개념과 구현체 비교
    * [Set](./CS/DataStructure/02_ADT/Set.md) - 집합(Set)의 개념과 특징
  * **03_Structure** (자료구조)
    * [HashTable](./CS/DataStructure/03_Structure/HashTable.md) - 해시 테이블 자료구조
    * **Tree/**
      * [Tree](./CS/DataStructure/03_Structure/Tree/Tree.md) - 트리 구조의 개념과 종류
      * [AdvancedTree_Overview](./CS/DataStructure/03_Structure/Tree/AdvancedTree_Overview.md) - 고급 트리 간략 정리
  * **04_Implementation** (구현체)
    * [Java_HashMap](./CS/DataStructure/04_Implementation/java/Java_HashMap.md) - Java HashMap 구현체
    * [Java_Hashtable](./CS/DataStructure/04_Implementation/java/Java_Hashtable.md) - 레거시 Hashtable 클래스
    * [Java_LinkedHashMap](./CS/DataStructure/04_Implementation/java/Java_LinkedHashMap.md) - 순서를 기억하는 Map
* [Algorithm](./CS/Algorithm/README.md) - 알고리즘
  * **Hash**
    * [Hash](./CS/Algorithm/Hash/Hash.md) - 해시 용어 설명 및 개념
    * [OpenAddressing](./CS/Algorithm/Hash/OpenAddressing.md) - 개방 주소법과 탐색 전략
  * [TimeSpaceComplexity](./CS/Algorithm/TimeSpaceComplexity.md) - 시간 복잡도와 공간 복잡도
  * [BinarySearch](./CS/Algorithm/BinarySearch.md) - 이분탐색 알고리즘
  * [TwoPointers](./CS/Algorithm/TwoPointers.md) - 투포인터 기법
* [Computer Architecture](./CS/Computer_Structure/README.md) - 컴퓨터 구조
  * [Register](./CS/Computer_Structure/Register.md) - CPU 레지스터의 종류와 역할
  * [CPU Clock, Core, Thread](./CS/Computer_Structure/cpu_clock_core_thread.md) - CPU 클럭 동작 원리와 멀티코어/멀티스레드 개념
  * [Structure of Assembly Instructions](./CS/Computer_Structure/StructureOfassembly_instructions.md) - 어셈블리 명령어 구조
  * [Source Code and Commands](./CS/Computer_Structure/SourceCode_and_Command.md) - 소스코드 실행 과정
  * [Instruction Level Parallelism](./CS/Computer_Structure/instruction_level_parallelism.md) - 명령어 병렬 처리 기법 (파이프라이닝, 슈퍼스칼라, 비순차적 처리)
  * [Computer Architecture Overview](./CS/Computer_Structure/TheBigPictureOfComputerArchitecture.md) - 컴퓨터 구성 요소 및 기본 구조 개요
  * [ALU and Control Unit](./CS/Computer_Structure/ArithmeticLogicUnit_and_ControlUnit.md) - 산술논리연산장치와 제어장치의 구조 및 역할
  * [Interrupt 및 예외 요약](./CS/Computer_Structure/interrupt.md) - CPU 인터럽트와 예외(동기/비동기) 처리 개념 요약
* [Operating System](./CS/Operating_System/README.md) - 운영체제

## Java ☕

* [Java](./Java/README.md) - 자바 학습 노트
* [Java101](./Java/101/README.md) - 자바 101
  * [WhyHere](./Java/101/README.md#why-here)
  * [JavaBasics](./Java/101/java_basics.md) - 자바 기초
  * [ObjectOrientedProgramming](./Java/101/object_oriented_programming.md) - 객체지향 프로그래밍
  * [ClassAndData](./Java/101/class_and_data.md) - 클래스와 데이터
  * [Constructors](./Java/101/constructors.md) - 생성자
  * [AccessModifiers](./Java/101/access_modifiers.md) - 접근 제어자
  * [VariableAndMethodLifecycle](./Java/101/variable_and_method_lifecycle.md) - 변수와 메서드 생명주기
  * [JVMMemoryAndStatic](./Java/101/jvm_memory_and_static.md) - JVM 메모리와 정적
  * [DifferentMachineInterpretationByOS](./Java/101/why_different_machine_interpretation_by_os.md) - OS별 머신 해석 차이
  * [FinalModifiers](./Java/101/final_modifiers.md) - final 제어자
  * [InheritanceAndSuper](./Java/101/Inheritance_and_super.md) - 상속과 super
  * [ObjectComparison](./Java/101/objectComparison.md) - 객체 비교 (== vs equals())

* [JavaAdvanced](./Java/Java_Advanced/README.md) - 자바 고급
  * [JavaBeanPropertyConvention](./Java/Java_Advanced/JavaBeanPropertyConvention.md) - 자바빈 프로퍼티 규약
  * [자바-람다-동작원리와-함수형-개념](./Java/Java_Advanced/자바-람다-동작원리와-함수형-개념.md) - Java에서의 함수형 패러다임: 람다의 내부 동작 원리와 일급 객체·고차 함수·동작 파라미터화의 관계
  * [자바LTS버전별변경사항](./Java/Java_Advanced/자바LTS버전별변경사항.md) - Java 8부터 25까지 LTS 버전별 핵심 변경사항과 비LTS 버전 요약

* [JavaQnA](./Java/QnA/README.md) - 자바 Q&A
  * [Concepts](./Java/QnA/concepts) - 개념 관련 질문
    * [JVMQuestions](./Java/QnA/concepts/jvm_questions.md) - JVM 질문 모음 (메모리, JIT 컴파일러)
    * [WhyChildTypeCannotReferenceParentType](./Java/QnA/concepts/why_child_cannot_reference_parent.md) - 자식 타입이 부모 타입을 참조할 수 없는 이유
    * [Interface vs Abstract Class](./Java/QnA/interface_vs_abstract.md) - 인터페이스와 추상클래스의 차이점 분석
  * [Syntax](./Java/QnA/syntax) - 문법 관련 질문 (예정)
  * [BestPractices](./Java/QnA/best_practices) - 모범 사례 질문 (예정)

## C++ 🔧

* [CPP101](./C++/101/README.md) - C++ 101
  * [OverviewAndHistory](./C++/101/cpp_overview_and_history.md) - 개요와 역사
  * [DevelopmentEnvironment](./C++/101/cpp_development_environment.md) - 개발 환경
    * [WindowsSetup](./C++/101/windows_setup.md) - 윈도우 설정
  * [BasicSyntax](./C++/101/cpp_basic_syntax.md) - 기본 문법
  * [ColonVsDoubleColon](./C++/101/cpp_colon_vs_double_colon.md) - 콜론 vs 이중 콜론
  * [DefineVsFinalStatic](./C++/101/cpp_define_vs_final_static.md) - Define vs Final Static
  * [MemoryManagement](./C++/101/cpp_memory_management.md) - 메모리 관리
  * [SeparateCompilation](./C++/101/cpp_separate_compilation.md) - 분할 컴파일

## Web 🌐

* [WebDevelopment](./Web/README.md) - 웹 개발
  * [Web101](./Web/basics/README.md) - 웹 기초
    * [ServletAndWASArchitecture](./Web/basics/servlet_was_architecture.md) - 서블릿과 WAS 아키텍처
  * [Network](./Web/network/README.md) - 네트워크
    * [InternetNetworkBasics](./Web/network/internet_network_basics.md) - 인터넷 통신과 네트워크 기초
    * [HTTPAndWeb](./Web/network/http_and_web.md) - HTTP와 웹
    * [HTTPStatusCodes](./Web/network/http_status_codes.md) - HTTP 상태 코드
    * [HTTPHeaders](./Web/network/http_headers.md) - HTTP 헤더
    * [HTTPHeadersPart1](./Web/network/http_headers_part1.md) - HTTP 헤더(1) - 기본 개념과 표현
    * [HTTPHeadersPart2](./Web/network/http_headers_part2.md) - HTTP 헤더(2) - 정보, 인증, 쿠키
    * [URIAndWebRequestFlow](./Web/network/uri_and_web_request_flow.md) - URI, 웹 브라우저 요청 흐름
    * [StatefulStateless](./Web/network/stateful_stateless.md) - Stateful, Stateless와 비연결성
    * [HTTPMethods](./Web/network/http_methods.md) - HTTP 메서드
    * [IdempotencyAndSafety](./Web/network/idempotency_and_safety.md) - 멱등성과 그보다 더 엄격한 상위 개념인 안전성(Safety)

## Spring 🌱

* [Spring101](./Spring/101/README.md) - 스프링 101
  * [SpringBootProjectSetup](./Spring/101/spring_boot_project_setup.md) - 스프링 부트 프로젝트 설정
  * [SpringBasics](./Spring/101/spring_basics.md) - 스프링 기초
  * [SpringOverview](./Spring/101/spring_overview.md) - 스프링 개요
  * [SpringBeansAndDependencyInjection](./Spring/101/spring_beans_and_dependency_injection.md) - 스프링 빈과 의존성 주입
  * [DataObjectsInSpring](./Spring/101/data_objects_in_spring.md) - 스프링의 데이터 객체
  * [DataObjectTypes](./Spring/101/DataObjectTypes.md) - 데이터 객체 타입의 진화와 구분
  * [SpringLayeredArchitecture](./Spring/101/spring_layered_architecture.md) - 스프링 계층형 아키텍처
  * [ModelVsEntityInSpring](./Spring/101/spring_model_vs_entity.md) - 모델 vs 엔티티
  * [AOP](./Spring/101/spring_aop.md) - 관점 지향 프로그래밍
  * [SpringEvolutionHistory](./Spring/101/spring_evolution_history.md) - 스프링 발전 역사
  * [AutowiredQualifierPrimary](./Spring/101/AutowiredQualifierPrimary.md) - Autowired, Qualifier, Primary로 의존성 주입 관리하기
  * [CustomAnnotation](./Spring/101/CustomAnnotation.md) - 스프링에서 커스텀 애노테이션 만들기
  * [MultipleBeanInjection](./Spring/101/MultipleBeanInjection.md) - 여러 빈 주입 받기(List, Map)
  * [AutomanualBeansGuideline](./Spring/101/AutomanualBeansGuideline.md) - 자동 주입과 수동 빈 등록의 실무 운영 기준
  * [BeanScopeAndProvider](./Spring/101/BeanScopeAndProvider.md) - 스프링 빈 스코프와 Provider의 이해와 활용
  * [WebScope](./Spring/101/WebScope.md) - 스프링 웹 스코프와 프록시 활용법
  
* [SpringMVC](./Spring/MVC/README.md) - 스프링 MVC 가이드
  * [SpringMVCEvolution](./Spring/MVC/Spring_MVC_Evolution.md) - 스프링 MVC의 발전 역사
  * [SpringMVCRequestMapping](./Spring/MVC/Spring_MVC_Request_Mapping.md) - 스프링 MVC 요청 매핑
  * [SpringMVCRequestParameter](./Spring/MVC/Spring_MVC_Request_Parameter.md) - 스프링 MVC 요청 파라미터
  * [SpringFilterAndInterceptor](./Spring/MVC/spring_filter_and_interceptor.md) - 스프링 필터와 인터셉터
  * [SpringMVCResponse](./Spring/MVC/Spring_MVC_Response.md) - 스프링 MVC 응답
  * [LoggingSLF4JLogback](./Spring/101/Logging_SLF4J_Logback.md) - SLF4J와 Logback을 이용한 로깅
  * [SOLIDPrinciples](./Spring/101/solid_principles.md) - SOLID 원칙

* [SpringCore](./Spring/Core/README.md) - 스프링 코어
  * [SpringContainer](./Spring/Core/Container/README.md) - 스프링 컨테이너
    * [SingletonContainer](./Spring/Core/Container/Singleton_Container.md) - 싱글톤 컨테이너
  * [SpringComponents](./Spring/Core/Component/README.md) - 스프링 컴포넌트
    * [ComponentScan](./Spring/Core/Component/Component_Scan.md) - 컴포넌트 스캔
  * [DependencyInjection](./Spring/Core/Injection/README.md) - 의존성 주입
    * [FieldInjectionAndTestIssues](./Spring/Core/Injection/FieldInjectionAndTestIssues.md) - 필드 주입과 테스트 이슈

* [SpringTools](./Spring/Tools/README.md) - 스프링 도구
  * [Lombok](./Spring/Tools/Lombok/README.md) - 롬복
    * [LombokGuide](./Spring/Tools/Lombok/LombokGuide.md) - 롬복 가이드

<!-- ## English

* [EnglishStudy](./English/README.md) - 영어 공부
  * [BasicEnglish](./English/basics/README.md) - 기초 영어
  * [PracticalEnglish](./English/practical/README.md) - 실용 영어 -->

## Database 🗃️

* [Database](./DB/README.md) - 데이터베이스
  * [Connection Pool](./DB/ConnectionPool.md) - 커넥션 풀의 개념과 장단점 분석
  * [Data Source](./DB/DataSource.md) - 데이터 소스 설정 및 관리
  * [Session](./DB/Session.md) - 세션 개념 및 정의
  * [Transaction and ACID](./DB/Transaction_ACID.md) - 트랜잭션과 ACID 속성 상세 설명
  * [Transaction Manager](./DB/TransactionManger.md) - 트랜잭션 매니저의 커넥션 관리 및 동작 원리
  * [Transaction Template](./DB/TransactionTemplate.md) - 프로그래밍 방식 트랜잭션 제어
  * [SQL Mapper and ORM](./DB/SQLMapper_and_ORM.md) - SQL Mapper와 ORM의 개념 및 차이점
  * [Spring DB Exception Abstraction](./DB/SpringDB_예외추상화.md) - Spring의 데이터베이스 예외 추상화와 체크예외 문제 해결

## DevOps 🚀

* [DevOps](./DevOps/README.md) - 데옵스 및 인프라
  * [Docker](./DevOps/Docker/README.md) - 도커 컨테이너 기술

## Terminology (용어 정리) 📖

* [Terminology](./Terminology/README.md) - 용어 정리 노트
  * 용어의 어원/기원, 일상·타 업계·개발 업계(개발자/QA/PM)에서의 사용법, 나만의 정의 정리
  * [Module](./Terminology/Module.md) - 모듈(부품 vs 규격, 인터페이스와의 관계)

## Troubleshooting 🐛

* [Troubleshooting](./Troubleshooting/README.md) - 트러블슈팅
  * [java.lang.NullPointerException at UserService.login](./Troubleshooting/java.lang.NullPointerException_at_UserService_login.md)
  * [JPA N+1 Post List Select](./Troubleshooting/JPA_NPlusOne_Post_List_Select.md)

## AI Agent 🤖

* [Claude](./Claude/README.md) - Claude Code 사용법과 개념 정리
  * [기본 사용법](./Claude/기본사용법.md) - Claude Code가 뭐고 어떻게 동작하는지, 기본 개념
  * [권한 모드](./Claude/권한모드.md) - 도구 실행을 얼마나 자동으로 승인할지 정하는 설정
  * [서브에이전트](./Claude/서브에이전트.md) - 작업을 별도 에이전트에 위임하는 방식
  * [훅](./Claude/훅.md) - 특정 이벤트에 맞춰 자동으로 명령을 실행하는 설정
  * [스킬](./Claude/스킬.md) - 슬래시 커맨드로 불러쓰는 재사용 가능한 작업 절차
  * [MCP 서버](./Claude/MCP서버.md) - 외부 도구와 서비스를 연결하는 표준 프로토콜
  * [프로젝트 메모리](./Claude/프로젝트메모리.md) - CLAUDE.md와 자동 메모리로 규칙과 교훈을 남기는 방법
  * [여러 세션으로 작업하기](./Claude/여러세션으로작업하기.md) - 한 프로젝트에서 세션을 여러 개 켜고 작업하는 방법론과 장단점


