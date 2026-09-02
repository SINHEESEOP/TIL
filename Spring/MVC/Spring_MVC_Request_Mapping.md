# 스프링 MVC - 요청 매핑과 컨트롤러 패턴

## 1. 요청 매핑 기본

### 요청 매핑이란?
- **정의**: 클라이언트 요청 URL을 애플리케이션의 특정 메서드에 매핑하는 과정
- **목적**: HTTP 요청이 들어왔을 때 어떤 컨트롤러가 처리할지 결정
- **핵심**: `@RequestMapping` 어노테이션과 그 파생 어노테이션들을 사용

### 기본 매핑 어노테이션

```java
// 기본 형태
@RequestMapping("/hello")
public String hello() {
    return "hello";
}

// HTTP 메서드 지정
@RequestMapping(value = "/hello", method = RequestMethod.GET)
public String helloGet() {
    return "hello-get";
}

// 간편 어노테이션
@GetMapping("/hello")
@PostMapping("/hello")
@PutMapping("/hello")
@DeleteMapping("/hello")
@PatchMapping("/hello")
```

### 매핑 조건 다양화

```java
// URL 경로 변수
@GetMapping("/users/{userId}")
public String user(@PathVariable("userId") String userId) {
    return "user " + userId;
}

// 다중 경로 변수
@GetMapping("/users/{userId}/orders/{orderId}")
public String getOrder(@PathVariable String userId, 
                      @PathVariable Long orderId) {
    return "user=" + userId + ", order=" + orderId;
}

// 특정 파라미터 조건
@GetMapping(value = "/users", params = "type=admin")
public String usersAdmin() {
    return "admin users";
}

// 특정 헤더 조건
@GetMapping(value = "/users", headers = "mode=dark")
public String usersDarkMode() {
    return "dark mode users";
}

// 미디어 타입 조건 (Content-Type)
@PostMapping(value = "/users", consumes = "application/json")
public String createUserJson() {
    return "create user with JSON";
}

// Accept 헤더 조건
@GetMapping(value = "/users", produces = "application/json")
public String getUsersJson() {
    return "get users as JSON";
}
```

## 2. HTTP 요청 매핑 패턴

### URL 패턴 매칭 방식

```java
// 1. 정확한 경로 매칭
@GetMapping("/users/login")  // /users/login

// 2. 경로 변수 매칭
@GetMapping("/users/{id}")   // /users/1, /users/abc 등

// 3. 와일드카드 매칭
@GetMapping("/files/**")     // /files, /files/a, /files/a/b 등

// 4. 정규식 패턴
@GetMapping("/files/{filename:.+}")  // .이 포함된 파일명 매칭

// 5. 접미사 패턴 매칭 (스프링 부트 기본 비활성화)
@GetMapping("/files/*.png")  // /files/image.png 등
```

### 요청 매핑 우선순위

1. **정확한 경로 매칭**이 가장 높은 우선순위
2. **경로 변수**가 다음 우선순위
3. **와일드카드 매칭**이 가장 낮은 우선순위

```java
@GetMapping("/users/new")     // 우선순위 1: 정확한 경로
@GetMapping("/users/{id}")    // 우선순위 2: 경로 변수
@GetMapping("/users/*")       // 우선순위 3: 와일드카드
```

## 3. 요청 매핑 실제 API 예시

### RESTful 리소스 설계 예시

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    // 전체 상품 목록 조회
    @GetMapping
    public List<ProductDto> getAllProducts() {
        // ...
    }
    
    // 특정 상품 조회
    @GetMapping("/{id}")
    public ProductDto getProduct(@PathVariable Long id) {
        // ...
    }
    
    // 상품 생성
    @PostMapping
    public ResponseEntity<ProductDto> createProduct(@RequestBody ProductDto productDto) {
        // ...
        return ResponseEntity.status(HttpStatus.CREATED).body(savedProduct);
    }
    
    // 상품 수정
    @PutMapping("/{id}")
    public ProductDto updateProduct(@PathVariable Long id, 
                                  @RequestBody ProductDto productDto) {
        // ...
    }
    
    // 상품 부분 수정
    @PatchMapping("/{id}")
    public ProductDto patchProduct(@PathVariable Long id, 
                                 @RequestBody Map<String, Object> updates) {
        // ...
    }
    
    // 상품 삭제
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        // ...
        return ResponseEntity.noContent().build();
    }
    
    // 상품 검색
    @GetMapping("/search")
    public List<ProductDto> searchProducts(@RequestParam String keyword) {
        // ...
    }
}
```

### 계층형 리소스 관계 매핑 예시

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    // 주문 목록 조회
    @GetMapping
    public List<OrderDto> getOrders() {
        // ...
    }
    
    // 특정 주문 조회
    @GetMapping("/{orderId}")
    public OrderDto getOrder(@PathVariable Long orderId) {
        // ...
    }
    
    // 주문 내 상품 목록 조회
    @GetMapping("/{orderId}/items")
    public List<OrderItemDto> getOrderItems(@PathVariable Long orderId) {
        // ...
    }
    
    // 주문 내 특정 상품 조회
    @GetMapping("/{orderId}/items/{itemId}")
    public OrderItemDto getOrderItem(@PathVariable Long orderId,
                                   @PathVariable Long itemId) {
        // ...
    }
    
    // 주문 상태 변경
    @PatchMapping("/{orderId}/status")
    public OrderDto updateOrderStatus(@PathVariable Long orderId,
                                    @RequestBody String status) {
        // ...
    }
}
```

## 4. 컨트롤러 구현 패턴

### 전통적인 @Controller 패턴

```java
@Controller
public class HomeController {
    
    // 뷰 이름 반환
    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("message", "Welcome home!");
        return "home";  // ViewResolver가 home.html 찾아 렌더링
    }
    
    // 리다이렉트
    @PostMapping("/redirect-demo")
    public String redirectDemo() {
        return "redirect:/home";  // 리다이렉트 응답 (300번대 상태 코드)
    }
    
    // 포워드
    @GetMapping("/forward-demo")
    public String forwardDemo() {
        return "forward:/home";  // 서버 내부에서 다른 URL로 전달
    }
}
```

### @RestController 패턴

```java
@RestController  // @Controller + @ResponseBody 결합
@RequestMapping("/api")
public class ApiController {
    
    // 단순 문자열 반환
    @GetMapping("/hello")
    public String hello() {
        return "Hello World";  // 응답 바디에 직접 출력
    }
    
    // 객체 반환 (JSON 변환)
    @GetMapping("/users/{id}")
    public UserDto getUser(@PathVariable Long id) {
        UserDto user = new UserDto();
        user.setId(id);
        user.setName("User " + id);
        return user;  // JSON으로 자동 변환
    }
}
```

### 요청/응답 유형별 패턴

```java
@RestController
@RequestMapping("/api/patterns")
public class PatternDemoController {
    
    // 1. 경로 변수 + 쿼리 파라미터
    @GetMapping("/users/{userId}")
    public UserDto getUserWithParams(
            @PathVariable Long userId,
            @RequestParam(required = false) String fields) {
        UserDto user = userService.findById(userId);
        
        // fields 파라미터로 필요한 필드만 선택 (예: ?fields=id,name)
        if (fields != null) {
            // 필드 필터링 로직
        }
        
        return user;
    }
    
    // 2. 요청 바디 + 응답 상태 코드
    @PostMapping("/resources")
    public ResponseEntity<ResourceDto> createResource(
            @RequestBody @Valid ResourceDto resourceDto) {
        
        ResourceDto created = resourceService.create(resourceDto);
        
        // 생성 응답 (201 Created)
        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(created.getId())
                .toUri();
        
        return ResponseEntity
                .created(location)  // 201 + Location 헤더
                .body(created);
    }
    
    // 3. 검증 오류 처리
    @PostMapping("/validate")
    public ResponseEntity<?> validateData(
            @RequestBody @Valid DataDto dataDto,
            BindingResult bindingResult) {
        
        if (bindingResult.hasErrors()) {
            Map<String, String> errors = new HashMap<>();
            bindingResult.getFieldErrors().forEach(error -> 
                errors.put(error.getField(), error.getDefaultMessage())
            );
            
            return ResponseEntity
                    .badRequest()  // 400 Bad Request
                    .body(errors);
        }
        
        // 검증 통과 후 처리
        return ResponseEntity.ok(dataService.process(dataDto));
    }
    
    // 4. 조건부 응답
    @GetMapping("/conditional/{id}")
    public ResponseEntity<ResourceDto> getConditional(
            @PathVariable Long id,
            @RequestHeader(value = "If-None-Match", required = false) String ifNoneMatch) {
        
        ResourceDto resource = resourceService.findById(id);
        String eTag = "\"" + resource.getVersion() + "\"";
        
        // ETag가 일치하면 304 Not Modified
        if (eTag.equals(ifNoneMatch)) {
            return ResponseEntity
                    .status(HttpStatus.NOT_MODIFIED)
                    .eTag(eTag)
                    .build();
        }
        
        // 변경된 리소스 반환
        return ResponseEntity
                .ok()
                .eTag(eTag)
                .body(resource);
    }
}
```

## 5. 요청 처리 고급 기법

### 핸들러 메서드 인터셉터

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoggingInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/admin/**");
        
        registry.addInterceptor(new AuthInterceptor())
                .addPathPatterns("/user/**", "/api/**");
    }
}

public class LoggingInterceptor implements HandlerInterceptor {
    
    private final Logger log = LoggerFactory.getLogger(getClass());
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
                            Object handler) throws Exception {
        log.info("Request URI: {}", request.getRequestURI());
        return true;  // true: 계속 진행, false: 요청 중단
    }
    
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, 
                          Object handler, ModelAndView modelAndView) throws Exception {
        log.info("Response status: {}", response.getStatus());
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, 
                               Object handler, Exception ex) throws Exception {
        if (ex != null) {
            log.error("Exception: {}", ex.getMessage());
        }
    }
}
```

### URL 경로 커스터마이징

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer
            .setUseSuffixPatternMatch(false)  // *.html 같은 접미사 패턴 비활성화
            .setUseTrailingSlashMatch(true);  // 후행 슬래시 매칭 활성화 (/users == /users/)
    }
    
    @Override
    public void addFormatters(FormatterRegistry registry) {
        // URL 경로 변수 변환을 위한 컨버터 등록
        registry.addConverter(new StringToEnumConverter());
    }
}

// 경로 변수를 enum 값으로 변환하는 커스텀 컨버터
public class StringToEnumConverter implements Converter<String, UserType> {
    
    @Override
    public UserType convert(String source) {
        return UserType.valueOf(source.toUpperCase());
    }
}

// API URL 버전 관리
@RestController
@RequestMapping("/api/v1")  // API 버전을 URL에 포함
public class ApiV1Controller {
    // ...
}

@RestController
@RequestMapping(path = "/api", 
               headers = "API-Version=2")  // 헤더로 버전 구분
public class ApiV2Controller {
    // ...
}
```

### 예외 처리 @ExceptionHandler

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {
    
    // 컨트롤러 내부의 예외 처리
    @ExceptionHandler(ProductNotFoundException.class)
    public ResponseEntity<Map<String, String>> handleProductNotFound(
            ProductNotFoundException ex) {
        
        Map<String, String> error = new HashMap<>();
        error.put("error", "Product not found");
        error.put("message", ex.getMessage());
        
        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(error);
    }
    
    @GetMapping("/{id}")
    public ProductDto getProduct(@PathVariable Long id) {
        return productService.findById(id)
                .orElseThrow(() -> new ProductNotFoundException("Product not found: " + id));
    }
}

// 전역 예외 처리
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<Map<String, String>> handleGlobalException(Exception ex) {
        Map<String, String> error = new HashMap<>();
        error.put("error", "Internal Server Error");
        error.put("message", ex.getMessage());
        
        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(
            MethodArgumentNotValidException ex) {
        
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        
        return ResponseEntity
                .status(HttpStatus.BAD_REQUEST)
                .body(errors);
    }
}
```

## 6. 원격 API 호출 및 통합 구현

### RestTemplate 활용

```java
@Service
public class ExternalApiService {
    
    private final RestTemplate restTemplate;
    
    public ExternalApiService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder
                .setConnectTimeout(Duration.ofSeconds(5))
                .setReadTimeout(Duration.ofSeconds(5))
                .build();
    }
    
    public UserDto getUserFromExternalApi(Long id) {
        String url = "https://api.example.com/users/{id}";
        return restTemplate.getForObject(url, UserDto.class, id);
    }
    
    public ResponseEntity<UserDto> createUserInExternalApi(UserDto user) {
        String url = "https://api.example.com/users";
        return restTemplate.postForEntity(url, user, UserDto.class);
    }
}

// 컨트롤러에서 활용
@RestController
@RequestMapping("/api/proxy")
public class ProxyController {
    
    private final ExternalApiService externalApiService;
    
    @Autowired
    public ProxyController(ExternalApiService externalApiService) {
        this.externalApiService = externalApiService;
    }
    
    @GetMapping("/users/{id}")
    public UserDto proxyGetUser(@PathVariable Long id) {
        return externalApiService.getUserFromExternalApi(id);
    }
}
```

### WebClient 활용 (비동기 API 호출)

```java
@Service
public class ReactiveApiService {
    
    private final WebClient webClient;
    
    public ReactiveApiService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder
                .baseUrl("https://api.example.com")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();
    }
    
    public Mono<UserDto> getUserReactive(Long id) {
        return webClient.get()
                .uri("/users/{id}", id)
                .retrieve()
                .bodyToMono(UserDto.class);
    }
    
    public Flux<UserDto> getAllUsersReactive() {
        return webClient.get()
                .uri("/users")
                .retrieve()
                .bodyToFlux(UserDto.class);
    }
}

// 컨트롤러에서 활용
@RestController
@RequestMapping("/api/reactive")
public class ReactiveController {
    
    private final ReactiveApiService reactiveApiService;
    
    @Autowired
    public ReactiveController(ReactiveApiService reactiveApiService) {
        this.reactiveApiService = reactiveApiService;
    }
    
    @GetMapping("/users/{id}")
    public Mono<UserDto> getUser(@PathVariable Long id) {
        return reactiveApiService.getUserReactive(id);
    }
    
    @GetMapping("/users")
    public Flux<UserDto> getAllUsers() {
        return reactiveApiService.getAllUsersReactive();
    }
}
``` 