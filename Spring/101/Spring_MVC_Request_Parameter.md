# 🚀 스프링 MVC - 요청 파라미터 처리

## 1️⃣ 파라미터 바인딩 개요

### 파라미터 바인딩 어노테이션 비교 📋

| 어노테이션 | 데이터 출처 | 바인딩 방식 | 주요 용도 |
|-----------|------------|------------|---------|
| `@RequestBody` | HTTP 요청 바디 | HTTP 메시지 컨버터 | JSON/XML 등 바디 데이터 처리 |
| `@ModelAttribute` | 쿼리 파라미터, 폼 데이터 | 프로퍼티 접근법 (setter) | 폼 제출, 단순 데이터 처리 |
| `@RequestParam` | 쿼리 파라미터, 폼 데이터 | 타입 변환기 | 단일 파라미터 처리 |
| `@PathVariable` | URL 경로 변수 | 타입 변환기 | 경로 기반 데이터 처리 |

## 2️⃣ 요청 바디 처리 - @RequestBody

### JSON 요청 바디 처리 📊

```java
@Slf4j
@RestController
public class RequestBodyController {
    
    // 도메인 객체 예시
    @Data
    static class UserRequest {
        private String username;
        private int age;
    }
    
    // @RequestBody 객체 매핑
    @PostMapping("/users")
    public String createUser(@RequestBody UserRequest userRequest) {
        log.info("username={}, age={}", userRequest.getUsername(), userRequest.getAge());
        // 비즈니스 로직 처리
        return "사용자 생성 완료";
    }
    
    // @RequestBody + 검증(Validation)
    @PostMapping("/users/validate")
    public String createUserWithValidation(
            @RequestBody @Valid UserRequest userRequest,
            BindingResult bindingResult) {
        
        if (bindingResult.hasErrors()) {
            log.info("검증 오류 발생: {}", bindingResult);
            return "검증 오류";
        }
        
        log.info("검증 성공: username={}, age={}", 
                userRequest.getUsername(), 
                userRequest.getAge());
        return "검증된 사용자 생성 완료";
    }
    
    // JSON 배열 처리
    @PostMapping("/users/batch")
    public String createUsers(@RequestBody List<UserRequest> userList) {
        userList.forEach(user -> {
            log.info("username={}, age={}", user.getUsername(), user.getAge());
        });
        return "배치 사용자 생성 완료";
    }
}
```

### @RequestBody 특징 🔍

- HTTP 요청 바디(body)의 내용을 객체로 변환
- **JSON → 객체** 변환에 HttpMessageConverter 사용 (`MappingJackson2HttpMessageConverter` 등)
- **필수값**으로 지정됨 (기본값: `required=true`) - 바디 없으면 400 오류
- `@Valid` 또는 `@Validated`와 함께 사용하여 검증 가능
- **주의**: QueryString 파라미터는 처리하지 않음
- **modelAttribute 나 RequestParam 처럼 생략 불가능**
    - 당연하지만, 미적용 시 RequestParam -> modelAttribute 순으로 인식하기 때문이며 대부분 body 부분은 생략 불가. 

## 3️⃣ 모델 파라미터 처리 - @ModelAttribute

### @ModelAttribute 사용 예시 🏷️

```java
@Controller
public class ModelAttributeController {
    
    // 도메인 객체 예시
    @Data 
    static class UserForm {
        private String username;
        private int age;
        private List<String> hobbies;
        private Address address;
    }
    
    @Data
    static class Address {
        private String city;
        private String street;
        private String zipcode;
    }
    
    // @ModelAttribute 명시적 사용
    @PostMapping("/signup")
    public String signup(@ModelAttribute("user") UserForm userForm, Model model) {
        // 비즈니스 로직 처리
        log.info("username={}, age={}", userForm.getUsername(), userForm.getAge());
        
        // Model에 추가 (생략 가능 - @ModelAttribute가 자동 추가)
        // model.addAttribute("user", userForm);
        
        return "user/signupComplete";  // 뷰 이름
    }
    
    // @ModelAttribute 생략 (객체)
    @PostMapping("/signup-simple")
    public String signupSimple(UserForm userForm) {  // @ModelAttribute 생략 가능
        log.info("간단 가입 처리: username={}, age={}", 
                userForm.getUsername(), userForm.getAge());
        return "user/signupComplete";
    }
    
    // 중첩된 객체 바인딩
    @PostMapping("/signup-address")
    public String signupWithAddress(@ModelAttribute UserForm userForm) {
        log.info("username={}, age={}", userForm.getUsername(), userForm.getAge());
        log.info("city={}, street={}, zipcode={}", 
                userForm.getAddress().getCity(),
                userForm.getAddress().getStreet(),
                userForm.getAddress().getZipcode());
        return "user/signupComplete";
    }
    
    // 컬렉션 바인딩 
    @PostMapping("/signup-hobbies")
    public String signupWithHobbies(@ModelAttribute UserForm userForm) {
        log.info("username={}, hobbies={}", 
                userForm.getUsername(), 
                userForm.getHobbies());
        return "user/signupComplete";
    }
    
    // 검증 추가
    @PostMapping("/signup-validate")
    public String signupWithValidation(
            @ModelAttribute @Valid UserForm userForm, 
            BindingResult bindingResult) {
        
        if (bindingResult.hasErrors()) {
            log.info("검증 오류 발생: {}", bindingResult);
            return "user/signupForm";  // 오류 발생 시 폼으로 돌아감
        }
        
        // 성공 로직
        return "user/signupComplete";
    }
    
    // 메서드 레벨 @ModelAttribute - 모든 핸들러에서 공통으로 사용
    @ModelAttribute("regions")
    public List<String> regions() {
        return List.of("서울", "경기", "강원", "제주");
    }
}
```

### @ModelAttribute 특징 🔍

- 요청 파라미터(쿼리 스트링, 폼 데이터)를 객체에 바인딩
- 별도 어노테이션 없이 **객체 타입 파라미터**가 있으면 @ModelAttribute가 자동 적용
- 바인딩 과정:
  1. 객체 생성 (기본 생성자 필요)
  2. 요청 파라미터 이름 = 객체 프로퍼티 이름인 경우 매핑
  3. 프로퍼티 타입에 맞게 값 변환 후 setter로 주입
- 자동으로 **Model에 추가됨** (뷰에서 바로 접근 가능)
- **타입 변환 오류**가 발생하면 `BindingResult`로 처리 가능

## 4️⃣ 단순 파라미터 처리 - @RequestParam

### 기본 사용법 📝

```java
@RestController
@RequestMapping("/params")
public class RequestParamController {
    
    // 기본 사용법
    @GetMapping("/basic")
    public String basic(@RequestParam("name") String name,
                       @RequestParam("age") int age) {
        log.info("name={}, age={}", name, age);
        return "name=" + name + ", age=" + age;
    }
    
    // 파라미터명 생략 (변수명 = 파라미터명)
    @GetMapping("/simple")
    public String simple(@RequestParam String name, @RequestParam int age) {
        log.info("name={}, age={}", name, age);
        return "name=" + name + ", age=" + age;
    }
    
    // 어노테이션 자체 생략 (단순 타입만 가능)
    @GetMapping("/implicit")
    public String implicit(String name, int age) {
        log.info("name={}, age={}", name, age);
        return "name=" + name + ", age=" + age;
    }
    
    // 필수값 지정
    @GetMapping("/required")
    public String required(
            @RequestParam(required = true) String name,
            @RequestParam(required = false) Integer age) {
        log.info("name={}, age={}", name, age);
        return "name=" + name + ", age=" + age;
    }
    
    // 기본값 지정
    @GetMapping("/default")
    public String defaultValue(
            @RequestParam(defaultValue = "guest") String name,
            @RequestParam(defaultValue = "-1") int age) {
        log.info("name={}, age={}", name, age);
        return "name=" + name + ", age=" + age;
    }
    
    // 값이 없을 때 기본값 지정
    @GetMapping("/default-if-empty")
    public String defaultIfEmpty(
            @RequestParam(defaultValue = "guest") String name,
            @RequestParam(defaultValue = "-1") int age,
            @RequestParam(defaultValue = "EMPTY") String param) {
        log.info("name={}, age={}, param={}", name, age, param);
        return "name=" + name + ", age=" + age + ", param=" + param;
    }
    
    // Map으로 모든 파라미터 받기
    @GetMapping("/map")
    public String map(@RequestParam Map<String, Object> paramMap) {
        log.info("paramMap={}", paramMap);
        return "paramMap=" + paramMap;
    }
    
    // 다중값 처리 - 배열
    @GetMapping("/array")
    public String array(@RequestParam String[] names) {
        log.info("names={}", (Object) names);
        return "names=" + Arrays.toString(names);
    }
    
    // 다중값 처리 - 리스트
    @GetMapping("/list")
    public String list(@RequestParam List<String> names) {
        log.info("names={}", names);
        return "names=" + names;
    }
}
```

### @RequestParam 고급 기능 📊

- **필수값 설정**: `required=true` (기본값) → 파라미터 없으면 400 오류
- **기본값 설정**: `defaultValue="값"` → 파라미터 없으면 기본값 사용
- **빈 문자열 처리**: `defaultValue`는 빈 문자열도 처리 가능
- **다중값 처리**: 
  - 같은 이름의 파라미터가 여러 개일 때 배열/컬렉션으로 처리
  - 예: `?ids=1&ids=2&ids=3` → `List<Integer> ids`로 처리 가능
- **MultiValueMap**: 파라미터별로 여러 값 처리

## 5️⃣ URL 경로 변수 처리 - @PathVariable

### 기본 사용법 🛣️

```java
@RestController
@RequestMapping("/api")
public class PathVariableController {
    
    // 기본 사용법
    @GetMapping("/users/{userId}")
    public String getUserById(@PathVariable String userId) {
        log.info("userId={}", userId);
        return "조회된 회원 ID = " + userId;
    }
    
    // 변수명이 다를 경우
    @GetMapping("/orders/{orderId}")
    public String getOrderById(@PathVariable("orderId") String orderNumber) {
        log.info("orderId={}", orderNumber);
        return "조회된 주문 번호 = " + orderNumber;
    }
    
    // 다중 경로 변수
    @GetMapping("/users/{userId}/orders/{orderId}")
    public String getUserOrder(
            @PathVariable String userId,
            @PathVariable Long orderId) {
        log.info("userId={}, orderId={}", userId, orderId);
        return "회원 ID = " + userId + ", 주문 ID = " + orderId;
    }
    
    // 정규식 패턴 사용
    @GetMapping("/files/{filename:.+}")
    public String getFile(@PathVariable String filename) {
        log.info("filename={}", filename);
        return "파일명 = " + filename;
    }
    
    // PathVariable + RequestParam 함께 사용
    @GetMapping("/users/{userId}/settings")
    public String getUserSettings(
            @PathVariable String userId,
            @RequestParam(required = false) String theme,
            @RequestParam(defaultValue = "ko") String language) {
        
        log.info("userId={}, theme={}, language={}", userId, theme, language);
        return "회원 ID = " + userId + ", 테마 = " + theme + ", 언어 = " + language;
    }
}
```

### @PathVariable 특징 🔍

- URL 경로의 일부를 변수로 사용
- RESTful API 설계에 필수적인 요소
- 값이 반드시 존재해야 함 (`required=true`가 기본값)
- **주의사항**:
  - 경로 변수명과 메서드 파라미터명이 일치해야 함 (다르면 명시적으로 지정)
  - 타입 변환이 불가능한 경우 400 오류 발생

## 6️⃣ 쿠키 값 처리 - @CookieValue

### 쿠키 처리 예시 🍪

```java
@RestController
public class CookieController {
    
    // 기본 사용법
    @GetMapping("/cookies/basic")
    public String getCookie(@CookieValue(name = "myCookie") String cookie) {
        log.info("myCookie={}", cookie);
        return "쿠키 값 = " + cookie;
    }
    
    // 필수 여부 설정
    @GetMapping("/cookies/optional")
    public String getOptionalCookie(
            @CookieValue(name = "myCookie", required = false) String cookie) {
        log.info("myCookie={}", cookie);
        return "쿠키 값 = " + (cookie != null ? cookie : "쿠키 없음");
    }
    
    // 기본값 설정
    @GetMapping("/cookies/default")
    public String getDefaultCookie(
            @CookieValue(name = "myCookie", defaultValue = "기본쿠키") String cookie) {
        log.info("myCookie={}", cookie);
        return "쿠키 값 = " + cookie;
    }
    
    // Cookie 객체로 직접 받기
    @GetMapping("/cookies/object")
    public String getCookieObject(@CookieValue(name = "myCookie") Cookie cookie) {
        log.info("쿠키 이름={}, 값={}, 도메인={}, 경로={}", 
                cookie.getName(), 
                cookie.getValue(), 
                cookie.getDomain(), 
                cookie.getPath());
        return "쿠키 객체 = " + cookie.getName() + ":" + cookie.getValue();
    }
    
    // 모든 쿠키 접근하기
    @GetMapping("/cookies/all")
    public String getAllCookies(HttpServletRequest request) {
        Cookie[] cookies = request.getCookies();
        if (cookies == null) {
            return "쿠키 없음";
        }
        
        StringBuilder result = new StringBuilder();
        for (Cookie cookie : cookies) {
            result.append(cookie.getName()).append("=")
                .append(cookie.getValue()).append("; ");
        }
        
        return "모든 쿠키 = " + result.toString();
    }
    
    // 쿠키 생성 예시
    @GetMapping("/cookies/create")
    public String createCookie(HttpServletResponse response) {
        Cookie cookie = new Cookie("createdCookie", "hello-cookie");
        cookie.setMaxAge(60 * 60); // 1시간
        cookie.setPath("/");
        
        response.addCookie(cookie);
        return "쿠키 생성 완료";
    }
}
```

### @CookieValue 특징 🔍

- HTTP 요청의 쿠키 값을 파라미터로 받음
- 필수값 설정: `required=true` (기본값)
- 기본값 설정: `defaultValue="값"`
- 쿠키는 문자열이지만 기본 타입 변환도 지원 (int, long 등)
- `Cookie` 객체로 직접 받을 수도 있음

## 7️⃣ 헤더 값 처리 - @RequestHeader

### 헤더 처리 예시 📨

```java
@RestController
public class HeaderController {
    
    // 단일 헤더 값 접근
    @GetMapping("/headers/basic")
    public String getHeader(@RequestHeader("User-Agent") String userAgent) {
        log.info("User-Agent={}", userAgent);
        return "User-Agent = " + userAgent;
    }
    
    // 필수 여부 설정
    @GetMapping("/headers/optional")
    public String getOptionalHeader(
            @RequestHeader(value = "X-Custom-Header", required = false) String customHeader) {
        log.info("X-Custom-Header={}", customHeader);
        return "X-Custom-Header = " + (customHeader != null ? customHeader : "헤더 없음");
    }
    
    // 기본값 설정
    @GetMapping("/headers/default")
    public String getDefaultHeader(
            @RequestHeader(value = "X-Custom-Header", defaultValue = "기본값") String customHeader) {
        log.info("X-Custom-Header={}", customHeader);
        return "X-Custom-Header = " + customHeader;
    }
    
    // 모든 헤더 맵으로 받기
    @GetMapping("/headers/map")
    public String getHeaderMap(@RequestHeader Map<String, String> headerMap) {
        headerMap.forEach((key, value) -> {
            log.info("header {}={}", key, value);
        });
        return "헤더 맵 크기 = " + headerMap.size();
    }
    
    // 다중값 헤더 처리
    @GetMapping("/headers/multi")
    public String getMultiValueHeader(
            @RequestHeader MultiValueMap<String, String> headerMap) {
        headerMap.forEach((key, values) -> {
            log.info("header {}={}", key, values);
        });
        return "다중값 헤더 맵 크기 = " + headerMap.size();
    }
    
    // 자주 사용하는 헤더 조합
    @GetMapping("/headers/common")
    public String getCommonHeaders(
            @RequestHeader("Host") String host,
            @RequestHeader("User-Agent") String userAgent,
            @RequestHeader(value = "Accept", required = false) String accept,
            @RequestHeader(value = "Accept-Language", required = false) String acceptLanguage,
            @RequestHeader(value = "Referer", required = false) String referer) {
        
        log.info("Host={}", host);
        log.info("User-Agent={}", userAgent);
        log.info("Accept={}", accept);
        log.info("Accept-Language={}", acceptLanguage);
        log.info("Referer={}", referer);
        
        return "일반적인 헤더 조회 완료";
    }
}
```

### @RequestHeader 특징 🔍

- HTTP 요청 헤더 값을 파라미터로 받음
- 필수값 설정: `required=true` (기본값)
- 기본값 설정: `defaultValue="값"`
- 모든 헤더 정보: `Map<String, String>` 또는 `MultiValueMap<String, String>`로 받을 수 있음
- **주의**: 헤더명은 대소문자를 구분하지 않음 (HTTP 스펙)
