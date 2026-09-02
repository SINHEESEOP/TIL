# 스프링 MVC - HTTP 응답 처리와 메시지 컨버터

## 1. HTTP 응답 - 정적 리소스와 뷰 템플릿

### 정적 리소스 제공

스프링 부트는 다음 디렉토리에 위치한 정적 리소스를 자동으로 제공합니다:
- `/static`
- `/public`
- `/resources`
- `/META-INF/resources`

#### 예시: `/static/hello.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>정적 리소스 예시</title>
</head>
<body>
    <h1>Hello, Static Resource!</h1>
</body>
</html>
```

**접근 방법**: `http://localhost:8080/hello.html`로 직접 접근 가능

#### 정적 리소스 설정 변경

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // /custom/** URL로 접근하면 classpath:/static/custom/ 위치의 리소스 제공
        registry.addResourceHandler("/custom/**")
                .addResourceLocations("classpath:/static/custom/");
    }
}
```

### 뷰 템플릿 활용

스프링 부트는 기본적으로 `/resources/templates/` 경로에서 템플릿을 찾습니다.

#### Thymeleaf 템플릿 예시: `/resources/templates/hello.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>뷰 템플릿 예시</title>
</head>
<body>
    <h1 th:text="${message}">기본 메시지</h1>
</body>
</html>
```

#### 컨트롤러 예시

```java
@Controller
public class ViewController {
    
    // 뷰의 논리 이름 반환
    @GetMapping("/hello-view")
    public String hello(Model model) {
        model.addAttribute("message", "Hello from Controller!");
        return "hello";  // templates/hello.html을 찾아 렌더링
    }
    
    // ModelAndView 사용
    @GetMapping("/hello-model-and-view")
    public ModelAndView helloModelAndView() {
        ModelAndView mav = new ModelAndView("hello");
        mav.addObject("message", "Hello from ModelAndView!");
        return mav;
    }
    
    // Void 반환 - URL 경로를 뷰 이름으로 사용
    @GetMapping("/hello-void")
    public void helloVoid(Model model) {
        model.addAttribute("message", "Hello from void!");
        // 뷰 이름: "hello-void"
    }
    
    // 리다이렉트
    @GetMapping("/redirect-demo")
    public String redirectDemo() {
        return "redirect:/hello-view";
    }
    
    // 포워드
    @GetMapping("/forward-demo")
    public String forwardDemo() {
        return "forward:/hello-view";
    }
}
```

## 2. HTTP 응답 - HTTP API와 메시지 바디

### @ResponseBody 활용

```java
@Controller
public class ResponseBodyController {
    
    // 단순 텍스트 응답
    @ResponseBody
    @GetMapping("/response-body-string")
    public String responseBodyString() {
        return "OK";  // 문자열을 HTTP 응답 바디에 직접 전송
    }
    
    // JSON 객체 응답
    @ResponseBody
    @GetMapping("/response-body-json")
    public HelloData responseBodyJson() {
        HelloData helloData = new HelloData();
        helloData.setUsername("user");
        helloData.setAge(20);
        return helloData;  // 객체가 JSON으로 변환되어 응답
    }
    
    // ResponseEntity 사용
    @GetMapping("/response-entity")
    public ResponseEntity<HelloData> responseEntity() {
        HelloData helloData = new HelloData();
        helloData.setUsername("user");
        helloData.setAge(20);
        
        return ResponseEntity
                .status(HttpStatus.OK)  // 상태 코드 설정
                .header("Custom-Header", "custom-value")  // 헤더 추가
                .body(helloData);  // 응답 데이터
    }
    
    // 조건에 따른 다양한 응답
    @GetMapping("/response-entity-dynamic")
    public ResponseEntity<?> responseEntityDynamic(
            @RequestParam(defaultValue = "true") boolean success) {
        
        if (success) {
            HelloData data = new HelloData();
            data.setUsername("success");
            data.setAge(30);
            
            return ResponseEntity.ok(data);
        } else {
            Map<String, String> error = new HashMap<>();
            error.put("code", "F001");
            error.put("message", "처리 중 오류 발생");
            
            return ResponseEntity
                    .status(HttpStatus.BAD_REQUEST)
                    .body(error);
        }
    }
    
    // 응답 상태 코드만 반환
    @GetMapping("/response-status")
    @ResponseStatus(HttpStatus.CREATED)  // 201 응답
    @ResponseBody
    public HelloData responseStatus() {
        HelloData helloData = new HelloData();
        helloData.setUsername("created");
        helloData.setAge(10);
        return helloData;
    }
}

@Data
static class HelloData {
    private String username;
    private int age;
}
```

### @RestController 활용

```java
@RestController  // @Controller + @ResponseBody
@RequestMapping("/api")
public class ApiController {
    
    @GetMapping("/hello")
    public String hello() {
        return "Hello API";  // 문자열 직접 반환
    }
    
    @GetMapping("/users/{id}")
    public UserDto getUser(@PathVariable Long id) {
        UserDto user = new UserDto();
        user.setId(id);
        user.setName("User " + id);
        user.setActive(true);
        return user;  // 객체 자동 JSON 변환 반환
    }
    
    @PostMapping("/users")
    public ResponseEntity<UserDto> createUser(@RequestBody UserDto userDto) {
        // 사용자 생성 로직 가정
        userDto.setId(1L);  // DB에서 할당된 ID 가정
        
        return ResponseEntity
                .status(HttpStatus.CREATED)
                .header("Location", "/api/users/" + userDto.getId())
                .body(userDto);
    }
    
    @Data
    static class UserDto {
        private Long id;
        private String name;
        private boolean active;
    }
}
```

## 3. HTTP 메시지 컨버터

### 메시지 컨버터란?

- **개념**: HTTP 요청/응답의 바디를 객체로 변환하거나, 객체를 HTTP 바디로 변환해주는 컴포넌트
- **적용 대상**: `@RequestBody`, `@ResponseBody`, `HttpEntity`, `ResponseEntity`
- **Spring MVC**: 기본적으로 다양한 메시지 컨버터를 제공

### 주요 메시지 컨버터 종류

```
[ HTTP 요청 ] -> [ 컨버터 ] -> [ 자바 객체 ]
[ 자바 객체 ] -> [ 컨버터 ] -> [ HTTP 응답 ]
```

| 메시지 컨버터 | 설명 | Content-Type | 클래스 타입 |
|--------------|------|--------------|------------|
| `StringHttpMessageConverter` | 문자열 처리 | `text/*` | `String` |
| `MappingJackson2HttpMessageConverter` | JSON 처리 | `application/json` | 객체, Map |
| `ByteArrayHttpMessageConverter` | 바이트 배열 처리 | `application/octet-stream` | `byte[]` |
| `FormHttpMessageConverter` | 폼 데이터 처리 | `application/x-www-form-urlencoded` | `MultiValueMap` |

### 컨버터 선택 과정

**HTTP 요청** (`@RequestBody`, `HttpEntity`)
1. 요청 헤더의 `Content-Type` 미디어 타입 확인
2. 컨버터 목록에서 해당 미디어 타입 지원하는지 확인
3. 컨버터의 `canRead()` 메서드로 읽을 수 있는지 확인 
4. 컨버터의 `read()` 메서드로 객체로 변환

**HTTP 응답** (`@ResponseBody`, `ResponseEntity`)
1. 반환 타입 확인
2. 컨버터의 `canWrite()` 메서드로 쓸 수 있는지 확인
3. 요청의 `Accept` 헤더 확인 (클라이언트가 받기 원하는 미디어 타입)
4. 컨버터의 `write()` 메서드로 응답 바디 생성

### 메시지 컨버터 사용 예시

```java
@RestController
@RequestMapping("/converters")
public class ConverterDemoController {
    
    // 문자열 처리 (StringHttpMessageConverter)
    @PostMapping("/string")
    public String stringConverter(@RequestBody String body) {
        return "Received: " + body;  // 문자열 그대로 반환
    }
    
    // JSON 처리 (MappingJackson2HttpMessageConverter)
    @PostMapping("/json")
    public UserData jsonConverter(@RequestBody UserData userData) {
        userData.setProcessed(true);
        return userData;  // 객체 -> JSON 변환
    }
    
    // 바이트 배열 처리 (ByteArrayHttpMessageConverter)
    @PostMapping("/bytes")
    public byte[] byteConverter(@RequestBody byte[] bytes) {
        // 바이트 배열 처리 (예: 이미지 데이터)
        return bytes;  // 바이트 배열 그대로 반환
    }
    
    // Accept 헤더에 따른 컨버터 선택
    @GetMapping(path = "/users/{id}", 
               produces = {"application/json", "application/xml"})
    public UserData getUserWithAccept(@PathVariable Long id) {
        UserData userData = new UserData();
        userData.setId(id);
        userData.setName("User by Accept");
        userData.setProcessed(true);
        return userData;  // Accept 헤더에 따라 JSON 또는 XML로 변환
    }
    
    // Content-Type에 따른 컨버터 선택
    @PostMapping(path = "/content-type", 
                consumes = {"application/json", "application/x-www-form-urlencoded"})
    public String handleByContentType(HttpEntity<Object> httpEntity) {
        Object body = httpEntity.getBody();
        return "Received content type: " + httpEntity.getHeaders().getContentType() 
             + ", body type: " + body.getClass().getSimpleName();
    }
    
    @Data
    static class UserData {
        private Long id;
        private String name;
        private boolean processed;
    }
}
```

### 메시지 컨버터 등록과 설정

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    // 메시지 컨버터 추가 및 설정
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        // 기존 컨버터 유지하면서 추가하려면 extendMessageConverters 사용
    }
    
    // 기존 컨버터를 유지하면서 새 컨버터 추가
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        // YAML 변환을 위한 커스텀 컨버터 추가 예시
        converters.add(new YamlHttpMessageConverter());
    }
    
    // Jackson 설정 변경
    @Bean
    public MappingJackson2HttpMessageConverter jsonConverter() {
        MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
        ObjectMapper objectMapper = new ObjectMapper();
        
        // Jackson 설정: 날짜 포맷, null 처리 등
        objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        
        converter.setObjectMapper(objectMapper);
        return converter;
    }
}
```

## 4. 요청 매핑 핸들러 어댑터 구조

### 스프링 MVC 요청 처리 구조

다음은 스프링 MVC가 요청을 처리하는 구조입니다:

```
HTTP 요청 → DispatcherServlet → HandlerMapping → 핸들러(컨트롤러) 조회
             ↓
HTTP 응답 ← DispatcherServlet ← 핸들러 어댑터 ← 핸들러(컨트롤러) 실행
```

### 핸들러 어댑터의 역할

- **목적**: 다양한 형태의 핸들러(컨트롤러)를 실행하기 위한 어댑터 패턴 적용
- **기능**: HTTP 요청 파라미터, 헤더, 바디 처리 및 변환
- **구현**: 다양한 종류의 핸들러 어댑터가 있음

### RequestMappingHandlerAdapter의 처리 흐름

```
HTTP 요청 → ArgumentResolver → 컨트롤러
             ↓
HTTP 응답 ← ReturnValueHandler ← 컨트롤러
```

#### ArgumentResolver (요청 파라미터 처리)

- **역할**: HTTP 요청 정보를 컨트롤러의 파라미터에 맞게 변환
- **지원 어노테이션**: `@RequestParam`, `@PathVariable`, `@RequestBody`, `@ModelAttribute` 등
- **처리 과정**: 
  1. 컨트롤러의 파라미터 정보 분석
  2. 적합한 ArgumentResolver 선택
  3. 파라미터 객체 생성 및 설정

#### ReturnValueHandler (응답 처리)

- **역할**: 컨트롤러의 반환값을 HTTP 응답으로 변환
- **지원 반환 타입**: `String`, `void`, `ModelAndView`, `@ResponseBody`, `ResponseEntity` 등
- **처리 과정**:
  1. 컨트롤러의 반환 타입 분석
  2. 적합한 ReturnValueHandler 선택
  3. HTTP 응답 생성

### 핵심 인터페이스와 구현체

```java
// HandlerMethodArgumentResolver 인터페이스 (ArgumentResolver)
public interface HandlerMethodArgumentResolver {
    // 해당 파라미터 지원 여부 확인
    boolean supportsParameter(MethodParameter parameter);
    
    // 실제 파라미터 값 생성
    Object resolveArgument(MethodParameter parameter, 
                          ModelAndViewContainer mavContainer,
                          NativeWebRequest webRequest, 
                          WebDataBinderFactory binderFactory) throws Exception;
}

// HandlerMethodReturnValueHandler 인터페이스 (ReturnValueHandler)
public interface HandlerMethodReturnValueHandler {
    // 해당 반환 타입 지원 여부 확인
    boolean supportsReturnType(MethodParameter returnType);
    
    // 실제 응답 처리
    void handleReturnValue(Object returnValue, 
                         MethodParameter returnType,
                         ModelAndViewContainer mavContainer,
                         NativeWebRequest webRequest) throws Exception;
}
```

### 사용자 정의 ArgumentResolver 예시

```java
// 커스텀 어노테이션
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser {
}

// 커스텀 ArgumentResolver 구현
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {
    
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // @LoginUser 어노테이션이 있고, 타입이 UserDto인 경우 지원
        return parameter.hasParameterAnnotation(LoginUser.class) 
            && parameter.getParameterType().equals(UserDto.class);
    }
    
    @Override
    public Object resolveArgument(MethodParameter parameter, 
                                 ModelAndViewContainer mavContainer,
                                 NativeWebRequest webRequest, 
                                 WebDataBinderFactory binderFactory) throws Exception {
        
        // 세션에서 로그인 사용자 정보 추출 (예제)
        HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
        HttpSession session = request.getSession(false);
        if (session == null) {
            return null;
        }
        
        return session.getAttribute("loginUser");
    }
}

// 설정에 등록
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new LoginUserArgumentResolver());
    }
}

// 컨트롤러에서 사용
@RestController
public class UserController {
    
    @GetMapping("/user/info")
    public UserDto getUserInfo(@LoginUser UserDto loginUser) {
        // 로그인 사용자 정보 사용
        return loginUser;
    }
}
```

## 5. 응답과 요청 처리의 종합적 이해

### 응답 형식에 따른 구분 요약

| 응답 형식 | 사용 방법 | 특징 |
|----------|----------|------|
| **정적 리소스** | 정적 파일을 URL로 직접 접근 | 변화가 없는 파일 그대로 전달 |
| **뷰 템플릿** | `return "view_name"` | 동적 HTML 렌더링 (Thymeleaf, JSP 등) |
| **HTTP API** | `@ResponseBody`, `@RestController` | JSON 등의 데이터 직접 응답 |

### 요청 파라미터 처리 요약

| 요청 데이터 유형 | 처리 방법 | 어노테이션 |
|---------------|----------|----------|
| **쿼리 파라미터** | `@RequestParam`, 기본 타입 파라미터 | `?name=value` 형식의 URL 쿼리 |
| **경로 변수** | `@PathVariable` | `/users/{id}` 형식의 URL 패스 |
| **폼 데이터** | `@RequestParam`, `@ModelAttribute` | POST form-data |
| **HTTP 메시지 바디** | `@RequestBody` | JSON, XML 등의 바디 데이터 |

### HTTP 요청/응답의 데이터 구성 요소

#### HTTP 요청 3요소
- **Headers**: 요청에 대한 메타 정보 (Content-Type, Accept 등)
- **URI/Query Params**: 리소스 식별 및 필터링 정보
- **Body**: 실제 전송하려는 데이터 (POST, PUT 등에서 주로 사용)

#### HTTP 응답 3요소
- **Status Code**: 요청 처리 결과 상태 코드 (200 OK, 404 Not Found 등)
- **Headers**: 응답에 대한 메타 정보 (Content-Type, Cache-Control 등)
- **Body**: 실제 응답 데이터 (HTML, JSON, 이미지 등)

### @PathVariable과 메시지 컨버터의 관계

- **@PathVariable**: URI 경로에서 값을 추출하는 단순 문자열 바인딩 메커니즘
  - 예: `/users/{id}` → `@PathVariable Long id`
  - 메시지 컨버터 작동하지 않음
  - 단순 타입 변환 (String → 기본 타입)만 발생

- **메시지 컨버터**: HTTP 요청/응답 바디 데이터 변환 전담
  - `@RequestBody`, `@ResponseBody`, `HttpEntity`, `ResponseEntity`에서만 작동
  - 전체 HTTP 메시지 바디를 객체로 또는 객체에서 메시지 바디로 변환

### produces, consumes, Accept, Content-Type 관계

| 항목 | 설명 | HTTP 관련 요소 |
|-----|------|--------------|
| **consumes** | 서버가 받을 수 있는 요청 형식 지정 | ↔ **Content-Type** 헤더와 맞아야 함 |
| **produces** | 서버가 응답할 수 있는 형식 지정 | ↔ **Accept** 헤더와 맞아야 함 |

```java
@PostMapping(
    consumes = "application/json",  // Content-Type: application/json 요청만 처리
    produces = "application/json"   // Accept: application/json 요청만 처리
)
```

### 메시지 컨버터 작동 어노테이션 정리

| 어노테이션 | 메시지 컨버터 작동 여부 |
|----------|---------------------|
| **@RequestBody** | 작동 (요청 바디 → 객체) |
| **@ResponseBody** | 작동 (객체 → 응답 바디) |
| **@PathVariable** | 비작동 (단순 문자열 바인딩) |
| **@RequestParam** | 비작동 (단순 문자열 바인딩) |
| **@ModelAttribute** | 비작동 (파라미터 바인딩 후 객체 생성) |

### 406 Not Acceptable 발생 원인

- **발생 상황**: 클라이언트의 Accept 헤더와 서버의 produces 속성이 호환되지 않을 때
- **예시**: 
  - 클라이언트: `Accept: application/xml`
  - 서버: `@GetMapping(produces = "application/json")`
  - 결과: 서버가 클라이언트가 원하는 형식을 제공할 수 없어 406 오류 발생

### Spring 컨트롤러의 응답 방식별 리턴 타입

| 리턴 타입 | 의미 | 예시 |
|---------|-----|------|
| **String** | 뷰 이름 | `return "home";` |
| **ModelAndView** | 뷰 + 데이터 | `return new ModelAndView("home");` |
| **void** | URL 기반 뷰 추론 | `// /hello URL → hello 뷰 사용` |
| **redirect:** | 302 리다이렉트 | `return "redirect:/home";` |
| **forward:** | 서버 내부 포워딩 | `return "forward:/home";` |
| **@ResponseBody** | 메시지 바디 응답 | `return userDto;` |
| **ResponseEntity** | 상태코드/헤더/바디 제어 | `return ResponseEntity.ok().body(data);` |

### 통합 예시: 완전한 REST API 컨트롤러

```java
@RestController
@RequestMapping("/api/v1/books")
public class BookApiController {
    
    private final BookService bookService;
    
    @Autowired
    public BookApiController(BookService bookService) {
        this.bookService = bookService;
    }
    
    // 1. 목록 조회 (쿼리 파라미터)
    @GetMapping
    public ResponseEntity<List<BookDto>> getBooks(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size,
            @RequestParam(required = false) String category) {
        
        List<BookDto> books = bookService.findBooks(page, size, category);
        return ResponseEntity.ok(books);
    }
    
    // 2. 단일 항목 조회 (경로 변수)
    @GetMapping("/{bookId}")
    public ResponseEntity<BookDto> getBook(@PathVariable Long bookId) {
        try {
            BookDto book = bookService.findBook(bookId);
            return ResponseEntity.ok(book);
        } catch (EntityNotFoundException e) {
            return ResponseEntity.notFound().build();
        }
    }
    
    // 3. 항목 생성 (JSON 요청 바디)
    @PostMapping
    public ResponseEntity<BookDto> createBook(
            @RequestBody @Valid BookDto bookDto,
            UriComponentsBuilder uriBuilder) {
        
        BookDto createdBook = bookService.createBook(bookDto);
        
        URI location = uriBuilder.path("/api/v1/books/{id}")
                .buildAndExpand(createdBook.getId()).toUri();
        
        return ResponseEntity.created(location).body(createdBook);
    }
    
    // 4. 항목 수정 (경로 변수 + JSON 요청 바디)
    @PutMapping("/{bookId}")
    public ResponseEntity<BookDto> updateBook(
            @PathVariable Long bookId,
            @RequestBody @Valid BookDto bookDto) {
        
        try {
            bookDto.setId(bookId);  // ID 설정
            BookDto updatedBook = bookService.updateBook(bookDto);
            return ResponseEntity.ok(updatedBook);
        } catch (EntityNotFoundException e) {
            return ResponseEntity.notFound().build();
        }
    }
    
    // 5. 항목 삭제 (경로 변수)
    @DeleteMapping("/{bookId}")
    public ResponseEntity<Void> deleteBook(@PathVariable Long bookId) {
        try {
            bookService.deleteBook(bookId);
            return ResponseEntity.noContent().build();
        } catch (EntityNotFoundException e) {
            return ResponseEntity.notFound().build();
        }
    }
    
    // 6. 예외 처리 - 전역 예외 핸들러 활용
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(
            MethodArgumentNotValidException ex) {
        
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        
        return ResponseEntity
                .badRequest()
                .body(errors);
    }
    
    // DTO 클래스
    @Data
    public static class BookDto {
        private Long id;
        
        @NotEmpty(message = "제목은 필수입니다")
        private String title;
        
        @NotEmpty(message = "저자는 필수입니다")
        private String author;
        
        private String category;
        private BigDecimal price;
        private LocalDate publishDate;
    }
}
```

## HttpEntity와 ResponseEntity

### HttpEntity 계층 구조

스프링의 HTTP 메시지 처리에서 핵심이 되는 클래스 계층 구조는 다음과 같습니다:

```
HttpEntity
  ├── RequestEntity
  └── ResponseEntity
```

### HttpEntity 기본 개념

- **정의**: HTTP 요청이나 응답의 **헤더와 바디**를 함께 표현하는 클래스
- **용도**: HTTP 메시지의 완전한 표현을 제공하기 위한 추상화
- **특징**: 불변(immutable) 객체로 설계되어 있음

```java
// 기본 사용 예시
HttpEntity<String> entity = new HttpEntity<>("Hello World", headers);
// 헤더만 포함
HttpEntity<Object> headersOnly = new HttpEntity<>(headers);
// 바디만 포함
HttpEntity<String> bodyOnly = new HttpEntity<>("Hello World");
```

### ResponseEntity 상세 활용법

ResponseEntity는 HttpEntity를 상속하며, HTTP 응답을 완전히 제어할 수 있는 다양한 기능을 제공합니다.

#### 1. 기본 사용법

```java
@GetMapping("/users/{id}")
public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
    UserDto user = userService.findById(id);
    return ResponseEntity.ok(user);
}
```

#### 2. 상태 코드 제어

```java
// 특정 상태 코드 설정
@PostMapping("/users")
public ResponseEntity<UserDto> createUser(@RequestBody UserDto userDto) {
    UserDto created = userService.save(userDto);
    return ResponseEntity.status(HttpStatus.CREATED).body(created);
}

// 상태 코드 메서드 활용
@GetMapping("/users/{id}")
public ResponseEntity<UserDto> getUserWithStatus(@PathVariable Long id) {
    try {
        UserDto user = userService.findById(id);
        return ResponseEntity.ok(user);  // 200 OK
    } catch (UserNotFoundException e) {
        return ResponseEntity.notFound().build();  // 404 Not Found
    } catch (Exception e) {
        return ResponseEntity.badRequest().build();  // 400 Bad Request
    }
}
```

#### 3. 헤더 커스터마이징

```java
@GetMapping("/users/{id}")
public ResponseEntity<UserDto> getUserWithHeaders(@PathVariable Long id) {
    UserDto user = userService.findById(id);
    
    return ResponseEntity.ok()
            .header("Custom-Header", "value")
            .header("Cache-Control", "max-age=3600")
            .body(user);
}
```

#### 4. 응답 위치 지정 (Resource URI)

```java
@PostMapping("/users")
public ResponseEntity<UserDto> createUserWithLocation(@RequestBody UserDto userDto,
                                                   UriComponentsBuilder ucb) {
    UserDto savedUser = userService.save(userDto);
    
    // location 헤더 생성
    URI location = ucb.path("/api/users/{id}")
                     .buildAndExpand(savedUser.getId())
                     .toUri();
    
    return ResponseEntity.created(location).body(savedUser);  // 201 Created
}
```

#### 5. 조건부 응답

```java
@GetMapping("/users/{id}")
public ResponseEntity<UserDto> getUserConditional(
        @PathVariable Long id,
        @RequestHeader(value = "If-Modified-Since", required = false)
        @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME) ZonedDateTime ifModifiedSince) {
    
    UserDto user = userService.findById(id);
    ZonedDateTime lastModified = user.getLastModified();
    
    // If-Modified-Since 헤더가 있고, 리소스가 변경되지 않았다면
    if (ifModifiedSince != null && !lastModified.isAfter(ifModifiedSince)) {
        return ResponseEntity.status(HttpStatus.NOT_MODIFIED).build();  // 304 Not Modified
    }
    
    return ResponseEntity.ok()
            .lastModified(lastModified.toInstant().toEpochMilli())
            .body(user);
}
```

#### 6. 타입 없는 응답

```java
@DeleteMapping("/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.delete(id);
    return ResponseEntity.noContent().build();  // 204 No Content
}
```

#### 7. 커스텀 응답 빌더 패턴 활용

```java
@GetMapping("/users")
public ResponseEntity<List<UserDto>> getUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size) {
    
    List<UserDto> users = userService.findAll(page, size);
    long totalItems = userService.count();
    
    return ResponseEntity.ok()
            .header("X-Total-Count", String.valueOf(totalItems))
            .header("X-Page", String.valueOf(page))
            .header("X-Size", String.valueOf(size))
            .contentType(MediaType.APPLICATION_JSON)
            .body(users);
}
```

### HttpEntity와 ResponseEntity의 관계 및 차이점

| 특성 | HttpEntity | ResponseEntity |
|------|-----------|---------------|
| **상속 관계** | 부모 클래스 | HttpEntity 상속 |
| **주요 용도** | HTTP 요청/응답 메시지 표현 | HTTP 응답 메시지 표현 |
| **상태 코드** | 제공하지 않음 | HTTP 상태 코드 포함 |
| **생성 방식** | 일반 생성자 | 빌더 패턴 지원 |
| **응답 커스터마이징** | 제한적 | 광범위한 커스터마이징 |
| **컨트롤러 반환값** | 가능하지만 제한적 | 일반적으로 권장됨 |

#### 핵심 차이점

1. **HttpEntity**는 요청과 응답 모두에 사용될 수 있는 일반적인 클래스입니다:
   ```java
   // 요청을 표현할 때
   HttpEntity<MyDto> requestEntity = new HttpEntity<>(myDto, headers);
   
   // 응답을 표현할 때
   return new HttpEntity<>(responseDto, responseHeaders);
   ```

2. **ResponseEntity**는 응답에 특화된 클래스로, 상태 코드와 함께 더 많은 기능을 제공합니다:
   ```java
   return ResponseEntity.status(HttpStatus.CREATED)
                      .headers(responseHeaders)
                      .body(responseDto);
   ```

### 실무 적용 시 권장 패턴

1. **요청 처리**: `@RequestBody`나 `HttpEntity`를 사용하여 요청 바디와 헤더에 접근
   ```java
   @PostMapping("/api/data")
   public ResponseEntity<ResultDto> processData(HttpEntity<RequestDto> httpEntity) {
       // 헤더 검증
       HttpHeaders headers = httpEntity.getHeaders();
       if (!headers.containsKey("X-API-KEY")) {
           return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();
       }
       
       // 요청 바디 처리
       RequestDto requestDto = httpEntity.getBody();
       ResultDto result = service.process(requestDto);
       
       return ResponseEntity.ok(result);
   }
   ```

2. **응답 생성**: 항상 `ResponseEntity`를 사용하여 응답 상태, 헤더, 바디를 명확하게 제어
   ```java
   @GetMapping("/api/resources/{id}")
   public ResponseEntity<?> getResource(@PathVariable Long id) {
       try {
           Resource resource = resourceService.findById(id);
           
           return ResponseEntity.ok()
                   .contentType(MediaType.APPLICATION_JSON)
                   .eTag(resource.getVersion().toString())
                   .lastModified(resource.getUpdatedAt().toEpochMilli())
                   .body(resource);
       } catch (ResourceNotFoundException e) {
           Map<String, String> error = Map.of("error", "Resource not found", 
                                            "id", id.toString());
           return ResponseEntity.status(HttpStatus.NOT_FOUND)
                              .body(error);
       }
   }
   ```

3. **예외 처리와 통합**: 글로벌 예외 핸들러와 함께 사용하여 일관된 오류 응답 형식 유지
   ```java
   @ControllerAdvice
   public class GlobalExceptionHandler {
       
       @ExceptionHandler(ResourceNotFoundException.class)
       public ResponseEntity<Map<String, Object>> handleResourceNotFound(
               ResourceNotFoundException ex) {
           Map<String, Object> body = new HashMap<>();
           body.put("timestamp", new Date());
           body.put("status", HttpStatus.NOT_FOUND.value());
           body.put("error", "Resource not found");
           body.put("message", ex.getMessage());
           
           return ResponseEntity.status(HttpStatus.NOT_FOUND).body(body);
       }
       
       // 다른 예외 처리 메서드들...
   }
   ```

이러한 접근 방식을 통해 HTTP 요청과 응답을 명확하게 처리하고, RESTful API의 품질을 향상시킬 수 있습니다.