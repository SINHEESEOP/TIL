# Spring Filter와 Interceptor 가이드

Spring에서 요청/응답을 가로채고 처리하는 두 가지 핵심 메커니즘을 이해하고 활용하는 방법을 정리한 문서입니다.

## 목차

1. [Filter와 Interceptor 개념](#-filter와-interceptor-개념)
2. [Filter vs Interceptor 차이점](#-filter-vs-interceptor-차이점)
3. [Filter 구현하기](#-filter-구현하기)
4. [Interceptor 구현하기](#-interceptor-구현하기)
5. [실제 활용 예제](#-실제-활용-예제)
6. [성능 고려사항](#-성능-고려사항)

## Filter와 Interceptor 개념

### Filter란?
- **Servlet 스펙에 정의된 요청/응답 가로채기 메커니즘**
- 웹 애플리케이션에 들어오는 모든 요청을 사전/사후 처리
- Spring Context 외부에서 동작

### Interceptor란?
- **Spring MVC 프레임워크에서 제공하는 요청 가로채기 메커니즘**
- HandlerMapping과 HandlerAdapter 사이에서 동작
- Spring Context 내부에서 동작하여 Spring Bean 접근 가능

## Filter vs Interceptor 차이점

| 구분 | Filter | Interceptor |
|------|----------|----------------|
| **동작 범위** | Servlet Container 레벨 | Spring MVC 레벨 |
| **동작 시점** | DispatcherServlet 이전/이후 | Handler 이전/이후 |
| **Spring Bean 접근** | 불가능 | 가능 |
| **예외 처리** | web.xml에서 처리 | @ControllerAdvice로 처리 |
| **URL 패턴** | 모든 요청 | 특정 Handler 매핑 |

### 요청 처리 흐름

```
클라이언트 요청
    ↓
Filter 1 (시작)
    ↓
Filter 2 (시작)
    ↓
DispatcherServlet
    ↓
Interceptor preHandle()
    ↓
Controller Handler 실행
    ↓
Interceptor postHandle()
    ↓
View 렌더링
    ↓
Interceptor afterCompletion()
    ↓
Filter 2 (종료)
    ↓
Filter 1 (종료)
    ↓
클라이언트 응답
```

## Filter 구현하기

### 1. Filter 인터페이스 구현

```java
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class LoggingFilter implements Filter {
    
    private static final Logger log = LoggerFactory.getLogger(LoggingFilter.class);
    
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 필터 초기화 로직
        log.info("LoggingFilter 초기화 완료");
    }
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        // 요청 처리 전 로깅
        long startTime = System.currentTimeMillis();
        log.info("요청 시작 - URL: {}, Method: {}", 
                httpRequest.getRequestURI(), httpRequest.getMethod());
        
        try {
            // 다음 필터 또는 서블릿으로 요청 전달
            chain.doFilter(request, response);
        } finally {
            // 응답 처리 후 로깅
            long duration = System.currentTimeMillis() - startTime;
            log.info("요청 완료 - 소요시간: {}ms, 상태코드: {}", 
                    duration, httpResponse.getStatus());
        }
    }
    
    @Override
    public void destroy() {
        // 필터 종료 시 정리 작업
        log.info("LoggingFilter 종료");
    }
}
```

### 2. Filter 등록 방법

#### 방법 1: @Component + @Order 사용
```java
@Component
@Order(1) // 필터 순서 지정
public class AuthenticationFilter implements Filter {
    // 구현 내용
}
```

#### 방법 2: FilterRegistrationBean 사용
```java
@Configuration
public class FilterConfig {
    
    @Bean
    public FilterRegistrationBean<LoggingFilter> loggingFilter() {
        FilterRegistrationBean<LoggingFilter> registrationBean = new FilterRegistrationBean<>();
        
        registrationBean.setFilter(new LoggingFilter()); // 필터 설정
        registrationBean.addUrlPatterns("/api/*"); // URL 패턴 지정
        registrationBean.setOrder(1); // 순서 지정
        
        return registrationBean;
    }
}
```

## Interceptor 구현하기

### 1. HandlerInterceptor 구현

```java
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

@Component
public class AuthInterceptor implements HandlerInterceptor {
    
    private static final Logger log = LoggerFactory.getLogger(AuthInterceptor.class);
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
                           Object handler) throws Exception {
        
        // 컨트롤러 실행 전 처리
        log.info("인증 체크 시작 - URI: {}", request.getRequestURI());
        
        String authToken = request.getHeader("Authorization");
        
        if (authToken == null || !isValidToken(authToken)) {
            // 인증 실패 시 요청 중단
            response.setStatus(HttpStatus.UNAUTHORIZED.value());
            response.getWriter().write("인증이 필요합니다");
            return false; // false 반환 시 요청 처리 중단
        }
        
        // 인증 성공 시 요청 계속 진행
        return true;
    }
    
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, 
                          Object handler, ModelAndView modelAndView) throws Exception {
        
        // 컨트롤러 실행 후, 뷰 렌더링 전 처리
        log.info("컨트롤러 실행 완료 - Handler: {}", handler.getClass().getSimpleName());
        
        if (modelAndView != null) {
            // 모델에 공통 데이터 추가
            modelAndView.addObject("serverTime", LocalDateTime.now());
        }
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, 
                               Object handler, Exception ex) throws Exception {
        
        // 뷰 렌더링 완료 후 처리
        if (ex != null) {
            log.error("요청 처리 중 오류 발생", ex);
        }
        
        log.info("요청 처리 완료 - 상태코드: {}", response.getStatus());
    }
    
    private boolean isValidToken(String token) {
        // 토큰 유효성 검증 로직
        return token.startsWith("Bearer ") && token.length() > 20;
    }
}
```

### 2. Interceptor 등록

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    
    @Autowired
    private AuthInterceptor authInterceptor;
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authInterceptor)
                .addPathPatterns("/api/**") // 적용할 URL 패턴
                .excludePathPatterns("/api/public/**") // 제외할 URL 패턴
                .order(1); // 인터셉터 순서 지정
    }
}
```

## 실제 활용 예제

### 1. 인증/인가 처리

```java
@Component
public class SecurityInterceptor implements HandlerInterceptor {
    
    @Autowired
    private UserService userService;
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
                           Object handler) throws Exception {
        
        // 어노테이션 기반 보안 체크
        if (handler instanceof HandlerMethod) {
            HandlerMethod handlerMethod = (HandlerMethod) handler;
            RequireAuth requireAuth = handlerMethod.getMethodAnnotation(RequireAuth.class);
            
            if (requireAuth != null) {
                String userId = extractUserIdFromToken(request);
                User user = userService.findById(userId);
                
                // 권한 체크
                if (!hasPermission(user, requireAuth.role())) {
                    response.setStatus(HttpStatus.FORBIDDEN.value());
                    return false;
                }
                
                // 사용자 정보를 request에 저장
                request.setAttribute("currentUser", user);
            }
        }
        
        return true;
    }
}
```

### 2. API 응답 시간 측정

```java
@Component
public class PerformanceInterceptor implements HandlerInterceptor {
    
    private static final String START_TIME = "startTime";
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
                           Object handler) throws Exception {
        
        // 시작 시간 기록
        request.setAttribute(START_TIME, System.currentTimeMillis());
        return true;
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, 
                               Object handler, Exception ex) throws Exception {
        
        // 응답 시간 계산 및 로깅
        Long startTime = (Long) request.getAttribute(START_TIME);
        if (startTime != null) {
            long duration = System.currentTimeMillis() - startTime;
            
            if (duration > 3000) { // 3초 이상 소요 시 경고
                log.warn("느린 응답 감지 - URI: {}, 소요시간: {}ms", 
                        request.getRequestURI(), duration);
            }
        }
    }
}
```

### 3. CORS 처리 Filter

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class CorsFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        
        // CORS 헤더 설정
        httpResponse.setHeader("Access-Control-Allow-Origin", "*");
        httpResponse.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
        httpResponse.setHeader("Access-Control-Allow-Headers", "Content-Type, Authorization");
        httpResponse.setHeader("Access-Control-Max-Age", "3600");
        
        // OPTIONS 요청 처리
        if ("OPTIONS".equalsIgnoreCase(httpRequest.getMethod())) {
            httpResponse.setStatus(HttpStatus.OK.value());
            return;
        }
        
        chain.doFilter(request, response);
    }
}
```

## 성능 고려사항

### 주의사항

1. **Filter는 모든 요청에 적용되므로 성능 영향 고려**
   - 무거운 작업은 피하고 빠른 처리 로직만 구현
   - 필요한 경우에만 URL 패턴 제한 사용

2. **Interceptor 예외 처리**
   - preHandle에서 false 반환 시 postHandle, afterCompletion 실행 안됨
   - 예외 발생 시에도 afterCompletion은 실행됨

3. **순서 지정의 중요성**
   - @Order나 order() 메서드로 실행 순서 명확히 지정
   - 보안 관련 필터/인터셉터는 높은 우선순위 설정

### 모범 사례

```java
// 좋은 예: 빠른 체크 후 조기 반환
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
                       Object handler) throws Exception {
    
    // 정적 리소스는 빠르게 통과
    if (request.getRequestURI().startsWith("/static/")) {
        return true;
    }
    
    // 필요한 경우에만 무거운 로직 실행
    return performAuthentication(request, response);
}
```

## 정리

**Filter 사용 시기:**
- 모든 요청에 대한 공통 처리 (로깅, 인코딩, CORS)
- Spring Context 외부에서 처리해야 하는 작업
- Servlet 레벨의 요청/응답 변경

**Interceptor 사용 시기:**
- Spring MVC 컨트롤러와 관련된 처리
- Spring Bean이 필요한 로직 (서비스, 리포지토리 등)
- 세밀한 URL 패턴 제어가 필요한 경우

올바른 선택을 통해 효율적이고 maintainable한 웹 애플리케이션을 구축하세요!