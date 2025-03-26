# Component Scan

## 컴포넌트 스캔이란?

컴포넌트 스캔은 스프링이 빈을 자동으로 등록하기 위한 메커니즘입니다. 개발자가 직접 `@Bean`으로 등록하지 않아도 스프링이 클래스패스를 스캔하여 자동으로 스프링 빈을 등록합니다.

## 📋 기본 개념

### 1. 컴포넌트 스캔과 의존관계 자동 주입

- `@ComponentScan`: 스프링 빈을 자동으로 등록
- `@Autowired`: 의존관계를 자동으로 주입
- 이점: 설정 코드 감소, 누락 방지

```java
@Configuration
@ComponentScan
public class AppConfig {
    // 빈 직접 등록 없이도 동작
}

@Component
public class MemberServiceImpl implements MemberService {
    private final MemberRepository memberRepository;
    
    @Autowired // 의존관계 자동 주입
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

### 2. 탐색 위치와 기본 스캔 대상

#### 탐색 위치 지정
```java
@ComponentScan(
    basePackages = "hello.core",
    basePackageClasses = AppConfig.class
)
```

- `basePackages`: 탐색할 패키지의 시작 위치 지정
- `basePackageClasses`: 지정한 클래스의 패키지를 탐색 시작 위치로 지정
- 지정하지 않으면: 설정 정보 클래스(`@ComponentScan`이 붙은 클래스)의 패키지가 시작 위치

#### 📌 실무 팁
- 패키지 위치를 지정하지 않고, 설정 정보 클래스를 프로젝트 최상단에 두는 것이 권장됨
- 스프링 부트의 `@SpringBootApplication`에 이미 `@ComponentScan` 포함

### 3. 컴포넌트 스캔 기본 대상

다음 애노테이션이 있는 클래스는 모두 스캔 대상:

| 애노테이션 | 설명 | 부가 기능 |
|---|---|---|
| `@Component` | 컴포넌트 스캔 대상 | 기본 |
| `@Controller` | 스프링 MVC 컨트롤러 | MVC 컨트롤러로 인식 |
| `@Service` | 비즈니스 로직 처리 | 특별한 처리 없음 (비즈니스 계층임을 표시) |
| `@Repository` | 데이터 접근 계층 | 데이터 계층 예외를 스프링 예외로 변환 |
| `@Configuration` | 설정 정보 | 스프링 설정 정보로 인식, 싱글톤 유지 |

각 애노테이션은 내부적으로 `@Component`를 포함하고 있음:

```java
@Component
public @interface Service {
}
```

### 4. 필터링 기능

특정 조건에 맞는 클래스만 스캔하거나 제외할 수 있습니다.

```java
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
```

#### 필터 타입 옵션

- `FilterType.ANNOTATION`: 기본값, 애노테이션을 인식해서 동작
- `FilterType.ASSIGNABLE_TYPE`: 지정한 타입과 자식 타입을 인식해서 동작
- `FilterType.ASPECTJ`: AspectJ 패턴 사용
- `FilterType.REGEX`: 정규 표현식
- `FilterType.CUSTOM`: `TypeFilter` 인터페이스 구현체 사용

### 5. 중복 등록과 충돌

#### 자동 등록 빈 간 충돌
- 이름이 같은 두 개의 빈이 자동 등록되면 스프링은 오류 발생
- `ConflictingBeanDefinitionException` 예외 발생

#### 수동 등록과 자동 등록 충돌
- 수동 빈이 우선권을 가짐 (수동 빈이 자동 빈을 오버라이딩)
- 스프링 부트에서는 기본적으로 오류 발생 (더 명확한 에러 메시지)
  ```
  Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
  ```

## 💡 실무 적용 팁

1. **프로젝트 구조** - 프로젝트 시작 루트 위치에 메인 설정 클래스를 두고 `@ComponentScan` 적용
   ```
   com.myproject
   ├── config
   │   └── AppConfig.java (@ComponentScan)
   ├── controller
   ├── service
   ├── repository
   └── MyProjectApplication.java
   ```

2. **명확한 의존관계** - 생성자 주입을 통해 의존관계를 명확히 드러내기
   ```java
   @Component
   public class OrderServiceImpl implements OrderService {
       private final MemberRepository memberRepository;
       private final DiscountPolicy discountPolicy;
       
       @Autowired
       public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
           this.memberRepository = memberRepository;
           this.discountPolicy = discountPolicy;
       }
   }
   ```

3. **충돌 방지** - 빈 이름을 명확히 지정하고, 중복되지 않도록 관리
   ```java
   @Component("memberService")
   public class MemberServiceImpl implements MemberService {
       // ...
   }
   ```

## 🔍 정리

- 컴포넌트 스캔은 자동으로 스프링 빈을 등록하는 편리한 기능
- `@Autowired`를 통한 의존관계 자동 주입으로 개발 효율성 증가
- 스캔 위치를 프로젝트 시작 루트에 두면 하위 패키지를 모두 스캔
- 빈 충돌에 주의하고 명확한 의존관계를 유지하는 것이 중요 