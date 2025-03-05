# 1. 프로젝트 환경 설정

이 문서는 스프링 부트 프로젝트를 처음 설정할 때 필요한 전반적인 환경 구성 과정을 다룹니다. 아래 각 챕터별로 자세한 내용과 핵심 요약을 포함하여 정리했습니다.


## 목차
1. 프로젝트 생성
2. 라이브러리 살펴보기
3. View 환경설정
4. 빌드하고 실행하기


## 1. 프로젝트 생성

### 내용 정리
- **사전 준비물**
  - **Java 17 이상 설치**: 스프링 부트 3.x 이상을 사용하기 위해 JDK 17 이상이 필요합니다.
  - **IDE**: IntelliJ 또는 Eclipse를 사용. (IntelliJ가 기본적으로 Gradle을 통한 실행 방식을 사용하지만, 실행 속도를 개선하기 위해 자바 직접 실행 방법도 있음)
  
- **스프링 부트 스타터 사이트 활용**
  - [https://start.spring.io](https://start.spring.io) 에 접속하여 프로젝트를 생성합니다.
  - **프로젝트 선택**:  
    - **Project**: Gradle - Groovy  
    - **Spring Boot**: 3.x.x (스프링 부트 3.0 이상 필수)  
    - **Language**: Java  
    - **Packaging**: Jar  
    - **Java**: 17 또는 21  
  - **Project Metadata 설정**:  
    - groupId: `hello`  
    - artifactId: `hello-spring`
  - **Dependencies 선택**:  
    - Spring Web, Thymeleaf (필요에 따라 추가 가능)

- **주의사항**
  - 반드시 스프링 부트 3.x 버전을 선택해야 하며, 이 버전부터는 **javax 패키지들이 jakarta 패키지로 변경**되어 사용됩니다.
  - 또한 H2 데이터베이스를 사용하는 경우, 버전 2.1.214 이상을 사용해야 합니다.

### 핵심 요약
- **JDK 17 이상**과 **스프링 부트 3.x 이상**이 필수 조건.
- [start.spring.io](https://start.spring.io)에서 Gradle 기반의 스프링 프로젝트를 생성.
- 프로젝트 메타데이터와 의존성(예: Spring Web, Thymeleaf) 선택에 주의.


## 2. 라이브러리 살펴보기

### 내용 정리
- **Gradle 의존관계 관리**
  - Gradle은 필요한 라이브러리(의존성)를 자동으로 다운로드하여 프로젝트에 포함시킵니다.
- **주요 스프링 부트 라이브러리**
  - **spring-boot-starter-web**: 스프링 웹 MVC, 내장 톰캣, REST API 개발 관련 라이브러리.
  - **spring-boot-starter-thymeleaf**: 타임리프 템플릿 엔진을 통한 뷰(View) 처리.
  - **spring-boot-starter**: 스프링 부트의 기본 구성 요소 (스프링 코어, 로깅 등 포함).
  - **테스트 라이브러리**:  
    - spring-boot-starter-test (JUnit, Mockito, AssertJ 등 포함)
- **라이브러리 의존성 구조**
  - 라이브러리 간 의존관계를 살펴보면, 각 스타터가 필요한 하위 모듈들을 함께 포함시켜 줍니다.

### 핵심 요약
- Gradle을 통해 스프링 부트 관련 주요 라이브러리들이 자동으로 관리됨.
- 웹, 템플릿(View), 테스트 등 각 기능에 맞는 스타터 라이브러리들을 사용.
- 의존성 관리를 통해 필요한 모듈들이 함께 다운로드되어 프로젝트에 포함됨.


## 3. View 환경설정

### 내용 정리
- **Welcome Page 구성**
  - `resources/static/index.html` 파일을 생성하면, 스프링 부트가 기본적으로 Welcome Page로 인식하여 제공.
  - HTML 파일에 간단한 내용과 링크 등을 포함할 수 있음.
  
- **Thymeleaf 템플릿 엔진**
  - 뷰(View) 렌더링을 위해 Thymeleaf 사용.
  - 컨트롤러에서 리턴한 문자열(view name)에 따라 `resources/templates/` 디렉터리 내에 해당 HTML 파일을 찾아 렌더링.
  - 예제:
    - **컨트롤러 코드**
      ```java
      @Controller
      public class HelloController {
          @GetMapping("hello")
          public String hello(Model model) {
              model.addAttribute("data", "hello!!");
              return "hello";
          }
      }
      ```
    - **템플릿 파일 (`resources/templates/hello.html`)**
      ```html
      <!DOCTYPE HTML>
      <html xmlns:th="http://www.thymeleaf.org">
      <head>
          <title>Hello</title>
          <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
      </head>
      <body>
          <p th:text="'안녕하세요. ' + ${data}">안녕하세요. 손님</p>
      </body>
      </html>
      ```
  - 뷰 리졸버(View Resolver)는 `templates/` 경로와 `.html` 확장자를 기본으로 매핑합니다.

- **참고 자료**
  - Thymeleaf 공식 사이트: [https://www.thymeleaf.org/](https://www.thymeleaf.org/)
  - 스프링 공식 튜토리얼: [https://spring.io/guides/gs/serving-web-content/](https://spring.io/guides/gs/serving-web-content/)

### 핵심 요약
- `static/index.html`을 통한 기본 Welcome Page 제공.
- Thymeleaf 템플릿 엔진을 사용하여 컨트롤러와 뷰가 매핑됨.
- 뷰 리졸버가 기본적으로 `resources/templates/` 디렉터리에서 HTML 파일을 찾음.


## 4. 빌드하고 실행하기

### 내용 정리
- **빌드 프로세스**
  - 콘솔(터미널)에서 `./gradlew build` 명령어를 통해 프로젝트를 빌드합니다.
  - 빌드 결과물은 `build/libs` 디렉터리에 생성된 JAR 파일입니다.
  
- **실행 방법**
  - 빌드된 JAR 파일을 실행:  
    ```bash
    java -jar hello-spring-0.0.1-SNAPSHOT.jar
    ```
  - 실행 후 브라우저에서 [http://localhost:8080/hello](http://localhost:8080/hello) 주소로 접근하여 동작을 확인합니다.
  
- **윈도우 사용자 주의사항**
  - 윈도우에서는 `gradlew.bat` 파일을 사용합니다.
  - Git Bash 터미널 사용 시 명령어 사용 방법에 주의.
  
- **IntelliJ 설정**
  - Gradle 대신 자바 직접 실행 설정으로 변경하면, 실행 속도가 개선됩니다.
  - IntelliJ의 **Project JDK**와 **Gradle JDK**가 올바른 (Java 17 이상) 버전으로 설정되어 있는지 확인 필요.
  - Windows에서는 `File > Settings`, Mac에서는 `IntelliJ IDEA > Preferences`에서 JDK 설정을 확인합니다.

### 핵심 요약
- **빌드**: `./gradlew build`로 프로젝트 빌드 후, JAR 파일 생성.
- **실행**: `java -jar` 명령어로 JAR 파일 실행, 웹 브라우저에서 동작 확인.
- **IDE 설정**: IntelliJ의 JDK 및 Gradle 설정이 Java 17 이상으로 맞춰져야 함.
- 윈도우 사용자라면 Gradle 배치 파일(`gradlew.bat`)과 Git Bash 사용법에 유의.


> **Tip**: 프로젝트 환경 설정 단계에서 사전 준비물과 IDE 설정, 라이브러리 관리 및 뷰 설정을 꼼꼼하게 확인하는 것이 스프링 부트 개발의 원활한 진행과 유지보수에 큰 도움이 됩니다.
