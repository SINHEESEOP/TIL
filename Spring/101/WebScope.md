# TIL: 스프링 웹 스코프와 프록시 활용법 🌐

> 웹 환경에서만 동작하는 특별한 스코프인 웹 스코프의 특징과 활용법을 정리했습니다. 특히 request 스코프와 프록시 모드를 중심으로 알아보겠습니다.

## 1. 웹 스코프란? 📌

- **정의** 💡  
  웹 환경에서만 동작하는 스코프로, HTTP 요청이나 세션 등의 생명주기와 연동되는 빈 스코프입니다.

- **웹 스코프 특징** ⚡  
  - 웹 환경에서만 동작 (웹 애플리케이션 컨텍스트 필요)
  - 스프링이 해당 스코프의 종료 시점까지 관리 (프로토타입과 다름)
  - 종료 시점에 @PreDestroy 같은 소멸 메서드가 실행됨

## 2. 웹 스코프 종류 🗂️

- **request** 📨  
  - HTTP 요청 하나가 들어오고 나갈 때까지 유지
  - 각 HTTP 요청마다 별도의 빈 인스턴스 생성

- **session** 🔑  
  - HTTP 세션과 동일한 생명주기
  - 사용자 단위로 빈이 관리됨

- **application** 📱  
  - 서블릿 컨텍스트(ServletContext)와 동일한 생명주기
  - 웹 애플리케이션당 하나의 빈이 생성됨

- **websocket** 🔌  
  - 웹소켓 연결이 생성되고 종료될 때까지 유지
  - 양방향 통신에서 사용

## 3. 웹 스코프 활용 예제 - request 스코프 📝

### 3.1 웹 환경 설정 ⚙️

- **의존성 추가 (Gradle)** 📦

```groovy
implementation 'org.springframework.boot:spring-boot-starter-web'
```

- **포트 변경 (선택 사항)** 🔢
```properties
# application.properties
server.port=9090
```

### 3.2 request 스코프 활용 시나리오 🎯

- **문제 상황** 🔍  
  - 여러 HTTP 요청이 동시에 들어올 때 각 요청을 구분해야 함
  - 로그에 요청 정보를 포함하고 싶음
  - 서비스 계층에 웹 관련 정보를 직접 전달하지 않고 싶음

- **기대하는 로그 형식** 📋  
  ```
  [UUID][요청URL] 메시지
  ```

### 3.3 웹 스코프 예제 코드 구현 💻

#### MyLogger 클래스 (request 스코프 빈)

```java
@Component
@Scope(value = "request")
public class MyLogger {
    private String uuid;
    private String requestURL;
    
    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }
    
    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }
    
    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }
    
    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}
```

- **핵심 코드 설명** 📝  
  - `@Scope(value = "request")`: 빈을 HTTP 요청당 하나씩 생성
  - `uuid`: 각 요청을 구분하는 고유 ID
  - `requestURL`: 현재 요청한 URL 정보
  - `@PostConstruct`: 빈 생성 시 UUID 생성
  - `@PreDestroy`: 요청 종료 시 로그 출력

#### 컨트롤러와 서비스

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}

@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger;
    
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

### 3.4 실행 시 발생하는 문제점 ⚠️

- **에러 메시지** ❌  
  ```
  Error creating bean with name 'myLogger': Scope 'request' is not active for the current thread; consider defining a scoped proxy for this bean if you intend to refer to it from a singleton;
  ```

- **문제 원인** 📝  
  - request 스코프는 HTTP 요청이 있어야 생성됨
  - 애플리케이션 시작 시점에는 HTTP 요청이 없어 빈을 생성할 수 없음
  - 싱글톤 빈(컨트롤러, 서비스)에 주입할 수 없음

## 4. 웹 스코프 문제 해결 방법 🛠️

### 4.1 Provider 사용 🔄

#### 컨트롤러 수정

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

#### 서비스 수정

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final ObjectProvider<MyLogger> myLoggerProvider;
    
    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

- **Provider 방식 특징** 💡  
  - 실제 빈 조회를 필요한 시점까지 지연(Lazy)
  - `getObject()` 호출 시점에는 HTTP 요청이 진행 중
  - 같은 HTTP 요청이면 같은 스프링 빈 반환
  - 구현이 복잡해짐 (매번 `getObject()` 호출 필요)

### 4.2 프록시 방식 사용 🧩

#### MyLogger 수정 (프록시 모드 적용)

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
    // 기존 코드와 동일
}
```

- **핵심 코드** 📝  
  - `proxyMode = ScopedProxyMode.TARGET_CLASS`: 클래스 기반 프록시 생성
  - 인터페이스 기반이라면 `ScopedProxyMode.INTERFACES` 사용

#### 컨트롤러와 서비스는 원래 코드로 복귀

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final MyLogger myLogger; // 프록시 주입
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}

@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger; // 프록시 주입
    
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

## 5. 프록시 동작 원리 🧠

### 5.1 CGLIB 프록시 객체

- 주입된 myLogger 클래스 확인
  ```java
  System.out.println("myLogger = " + myLogger.getClass());
  ```

- 출력 결과
  ```
  myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$b68b726d
  ```

### 5.2 프록시 작동 과정 🔄

1. 스프링 컨테이너는 CGLIB 라이브러리를 사용해 MyLogger를 상속받은 가짜 프록시 객체 생성
2. 스프링 컨테이너에 "myLogger"라는 이름으로 프록시 객체를 등록
3. 의존관계 주입 시 가짜 프록시 객체가 주입됨
4. 프록시 객체는 요청이 올 때까지 대기
5. 실제 요청 시 프록시 객체의 메서드가 호출되면 내부에서 진짜 request 스코프 빈을 요청
6. 프록시 객체는 원본 클래스를 상속받았기 때문에 클라이언트는 원본과 동일하게 사용 가능

```
Client -> Proxy Object -(위임)-> 실제 MyLogger 빈 (request 스코프)
```

### 5.3 특징 요약 📊

- **프록시 객체** 🎭  
  - 싱글톤처럼 동작하는 가짜 객체
  - 내부에 진짜 객체를 찾는 위임 로직만 존재
  - 실제 요청이 오면 그때 진짜 빈을 요청

- **지연 로딩** ⏳  
  - Provider와 프록시 모두 실제 객체 조회를 필요한 시점까지 지연
  - 프록시는 설정만으로 지연 로딩 구현 가능

- **다형성 활용** 🧩  
  - 애노테이션 설정만으로 원본 객체를 프록시로 대체
  - DI 컨테이너의 강점

## 6. 실무 활용 가이드 💼

### 6.1 주의 사항 ⚠️

- 마치 싱글톤처럼 보이지만 다르게 동작
- 특별한 스코프는 꼭 필요한 곳에만 최소화해서 사용
- 무분별한 사용은 유지보수를 어렵게 함

### 6.2 활용 사례 🎯

- 로그 추적기 (각 HTTP 요청별 처리 과정 추적)
- 사용자별 설정 정보 (세션 스코프)
- 공통 웹 애플리케이션 설정 (application 스코프)
- 실시간 통신 상태 관리 (websocket 스코프)

### 6.3 선택 가이드 🧭

1. **Provider 방식**
   - 명시적인 코드를 선호하는 경우
   - 빈을 지연해서 조회하는 시점을 정확히 제어하고 싶을 때

2. **프록시 방식**
   - 코드를 간결하게 유지하고 싶을 때
   - 기존 코드를 수정하지 않고 스코프 동작을 변경하고 싶을 때

## 7. 핵심 요약 📚

- **웹 스코프**
  - 웹 환경에서만 동작하는 특별한 스코프
  - 스프링이 생성부터 소멸까지 관리
  - 종료 시점에 @PreDestroy 호출됨

- **request 스코프**
  - HTTP 요청마다 별도의 빈 인스턴스 생성
  - 웹 관련 정보를 모든 계층에 파라미터로 전달하지 않아도 됨
  - 코드 계층 분리와 유지보수에 유리

- **활용 전략**
  - Provider로 지연 로딩
  - 프록시로 간결한 코드 유지
  - 필요한 곳에만 최소화해서 사용 