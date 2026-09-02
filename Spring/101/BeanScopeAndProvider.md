# TIL: 스프링 빈 스코프와 Provider의 이해와 활용

> 스프링 빈이 존재할 수 있는 범위(스코프)에 대한 개념과 종류, 그리고 프로토타입 스코프를 활용할 때 발생하는 문제점과 해결책을 정리했습니다.

## 1. 빈 스코프란?

- **정의**
  스프링 빈이 생성되고 존재할 수 있는 범위를 의미합니다.

- **기본 스코프**
  기본적으로 모든 스프링 빈은 **싱글톤 스코프**로 생성됩니다.

## 2. 스프링이 지원하는 스코프 종류

- **싱글톤(Singleton)**
  - 스프링 컨테이너 시작과 종료까지 유지되는 가장 넓은 범위
  - 기본 스코프로 별도 설정 없이 사용됨

- **프로토타입(Prototype)**
  - 스프링 컨테이너는 빈 생성과 의존관계 주입까지만 관여
  - 이후 관리는 클라이언트에 위임하는 매우 짧은 범위

- **웹 관련 스코프**
  - **request**: 웹 요청이 들어오고 나갈 때까지 유지
  - **session**: 웹 세션이 생성되고 종료될 때까지 유지
  - **application**: 웹의 서블릿 컨텍스트와 같은 범위로 유지

## 3. 빈 스코프 지정 방법

- **컴포넌트 스캔으로 자동 등록**

```java
@Scope("prototype")
@Component
public class HelloBean {}
```

- **설정 클래스에서 수동 등록**

```java
@Scope("prototype")
@Bean
PrototypeBean helloBean() {
    return new HelloBean();
}
```

## 4. 싱글톤 스코프와 프로토타입 스코프 비교

### 싱글톤 스코프 특징

- **생명주기**
  - 스프링 컨테이너 시작 시 생성
  - 스프링 컨테이너 종료 시까지 유지

- **인스턴스**
  - 항상 같은 인스턴스를 반환
  - 상태를 공유하므로 stateless 설계 필요

- **동작 과정**
  1. 스프링 컨테이너 생성 시 빈 초기화
  2. 컨테이너에서 빈 요청 시 항상 같은 객체 반환
  3. 컨테이너 종료 시 빈의 소멸 메서드(@PreDestroy) 실행

```java
// 싱글톤 빈 테스트 예시
@Test
public void singletonBeanFind() {
    // 컨테이너 생성
    AnnotationConfigApplicationContext ac = 
        new AnnotationConfigApplicationContext(SingletonBean.class);
    
    // 빈 두 번 조회
    SingletonBean bean1 = ac.getBean(SingletonBean.class);
    SingletonBean bean2 = ac.getBean(SingletonBean.class);
    
    // 동일한 인스턴스인지 검증
    assertThat(bean1).isSameAs(bean2);
    
    ac.close(); // 종료
}
```

### 프로토타입 스코프 특징

- **생명주기**
  - 스프링 컨테이너에서 빈 조회 시점에 생성
  - 생성, 의존관계 주입, 초기화까지만 스프링이 관여
  - 이후 관리는 클라이언트에 위임

- **인스턴스**
  - 조회할 때마다 항상 새로운 인스턴스 생성
  - 상태를 공유하지 않음

- **동작 과정**
  1. 컨테이너에서 빈 요청 시 새 객체 생성
  2. 의존관계 주입 및 초기화
  3. 클라이언트에 반환 후 컨테이너는 더 이상 관리하지 않음
  4. 소멸 메서드(@PreDestroy) 실행되지 않음

```java
// 프로토타입 빈 테스트 예시
@Test
public void prototypeBeanFind() {
    // 컨테이너 생성
    AnnotationConfigApplicationContext ac = 
        new AnnotationConfigApplicationContext(PrototypeBean.class);
    
    // 빈 두 번 조회
    PrototypeBean bean1 = ac.getBean(PrototypeBean.class);
    PrototypeBean bean2 = ac.getBean(PrototypeBean.class);
    
    // 서로 다른 인스턴스인지 검증
    assertThat(bean1).isNotSameAs(bean2);
    
    ac.close(); // 종료 (소멸 메서드 실행 안됨)
}
```

## 5. 프로토타입 스코프의 문제점

### 싱글톤 빈과 함께 사용 시 문제

- **의도치 않은 동작**
  - 싱글톤 빈이 프로토타입 빈을 주입받으면 최초 한 번만 주입됨
  - 싱글톤 빈이 살아있는 동안 같은 프로토타입 빈을 계속 사용하게 됨
  - 프로토타입 빈을 사용할 때마다 새 인스턴스를 받는 것이 아님!

```java
// 문제 상황 예시
@Test
void singletonClientUsePrototype() {
    // 설정
    AnnotationConfigApplicationContext ac = new 
        AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
    
    // 첫 번째 클라이언트 빈 조회 및 로직 실행
    ClientBean clientBean1 = ac.getBean(ClientBean.class);
    int count1 = clientBean1.logic();
    assertThat(count1).isEqualTo(1); // 카운트는 1
    
    // 두 번째 클라이언트 빈 조회 및 로직 실행
    ClientBean clientBean2 = ac.getBean(ClientBean.class);
    int count2 = clientBean2.logic();
    assertThat(count2).isEqualTo(2); // 카운트는 2 (같은 프로토타입 빈 사용)
}

// 싱글톤 클라이언트 빈
static class ClientBean {
    private final PrototypeBean prototypeBean; // 프로토타입 빈을 주입받음
    
    @Autowired
    public ClientBean(PrototypeBean prototypeBean) {
        this.prototypeBean = prototypeBean;
    }
    
    public int logic() {
        prototypeBean.addCount();
        return prototypeBean.getCount();
    }
}
```

- **원인**
  - 싱글톤 빈은 생성 시점에만 의존관계 주입이 일어남
  - 프로토타입 빈은 주입 시점에 새로 생성되지만, 이후에는 새로 생성되지 않음
  - 결과적으로 싱글톤 빈 내부의 프로토타입 빈은 싱글톤처럼 동작

## 6. 프로토타입 스코프 문제 해결책

### 1. ApplicationContext 직접 사용

```java
// ApplicationContext를 주입받아 직접 조회
static class ClientBean {
    @Autowired
    private ApplicationContext ac;
    
    public int logic() {
        // 사용할 때마다 스프링 컨테이너에 새로 요청
        PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
        prototypeBean.addCount();
        return prototypeBean.getCount();
    }
}
```

- **장점**
  - 매번 새로운 프로토타입 빈 생성 보장
  
- **단점**
  - 스프링 컨테이너에 종속적인 코드
  - 단위 테스트 어려움

### 2. ObjectProvider/ObjectFactory 사용

```java
// ObjectProvider 사용
static class ClientBean {
    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeanProvider;
    
    public int logic() {
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount();
        return prototypeBean.getCount();
    }
}
```

- **특징**
  - ObjectFactory: 기본 기능만 제공
  - ObjectProvider: ObjectFactory 상속, 편의 기능 추가
  - 스프링 의존적이지만 기능이 단순해 테스트 용이
  - 별도 라이브러리 필요 없음

### 3. JSR-330 Provider 사용

```java
// JSR-330 Provider 사용 (스프링 부트 3.0 미만)
@Autowired
private Provider<PrototypeBean> provider;

public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    return prototypeBean.getCount();
}
```

```java
// 스프링 부트 3.0 이상
// jakarta.inject:jakarta.inject-api:2.0.1 라이브러리 추가 필요
@Autowired
private jakarta.inject.Provider<PrototypeBean> provider;
```

- **특징**
  - 자바 표준(JSR-330)
  - 기능이 단순(get() 메서드만 존재)
  - 스프링이 아닌 다른 컨테이너에서도 사용 가능
  - 별도 라이브러리 추가 필요
    - 스프링 부트 3.0 미만: javax.inject:javax.inject:1
    - 스프링 부트 3.0 이상: jakarta.inject:jakarta.inject-api:2.0.1

## 7. 실무 활용 가이드

### 언제 프로토타입 스코프를 사용할까?

- 매번 사용할 때마다 새로운 객체가 필요한 경우
- 의존관계 주입이 완료된 새로운 객체가 필요한 경우

### 실무에서는?

- 싱글톤으로 대부분의 문제 해결 가능
- 프로토타입 빈을 직접 사용하는 경우는 매우 드묾
- Provider는 DL이 필요한 모든 경우에 사용 가능

### 선택 가이드

- 스프링 의존적 코드를 작성해도 괜찮다면
  → ObjectProvider (편의성 높음)
- 다른 컨테이너에서도 코드를 사용해야 한다면
  → JSR-330 Provider (자바 표준)
- 별도 기능이 필요 없고 스프링에 의존해도 된다면
  → 스프링이 제공하는 기능 사용

## 8. 핵심 포인트 요약

- **싱글톤 빈**
  - 컨테이너 시작부터 종료까지 하나의 인스턴스 유지
  - 상태를 공유하므로 stateless 설계 필요
  - @PreDestroy 같은 종료 메서드 호출됨

- **프로토타입 빈**
  - 조회할 때마다 새로운 인스턴스 생성
  - 스프링은 생성, 의존관계 주입, 초기화까지만 관여
  - @PreDestroy 같은 종료 메서드 호출되지 않음
  - 클라이언트가 관리 책임을 가짐

- **문제 해결 방법**
  - ApplicationContext 직접 사용 (비권장)
  - ObjectProvider/ObjectFactory 사용 (스프링 의존)
  - JSR-330 Provider 사용 (자바 표준)

- **DL (Dependency Lookup, 의존관계 조회)**
  - 의존관계를 외부 주입이 아닌 직접 필요한 의존관계를 찾는 방식
  - Provider는 DL 기능만 제공하는 가벼운 컨테이너

## 9. DL(Dependency Lookup)의 이해

### 9.1 DI와 DL의 차이점

- **DI (Dependency Injection)**
  - 의존관계를 외부(스프링 컨테이너)에서 주입받는 방식
  - 객체가 필요한 의존성을 직접 찾지 않고 수동적으로 받기만 함
  - 생성자, 수정자, 필드 주입 등으로 구현
  - 코드 예시:
    ```java
    @Service
    public class UserService {
        private final UserRepository userRepository;
        
        // 스프링이 UserRepository를 주입해줌 (DI)
        public UserService(UserRepository userRepository) {
            this.userRepository = userRepository;
        }
    }
    ```

- **DL (Dependency Lookup)**
  - 의존관계를 필요한 시점에 직접 컨테이너에 요청하여 찾는 방식
  - 객체가 능동적으로 의존성을 찾음
  - 지연 로딩(Lazy Loading) 구현에 유용
  - 코드 예시:
    ```java
    @Service
    public class UserService {
        private final ApplicationContext context;
        
        public UserService(ApplicationContext context) {
            this.context = context;
        }
        
        public void process() {
            // 필요한 시점에 직접 빈을 찾음 (DL)
            UserRepository repository = context.getBean(UserRepository.class);
            repository.save();
        }
    }
    ```

### 9.2 DL이 필요한 상황

1. **프로토타입 스코프 빈**
   - 싱글톤 빈에서 매번 새로운 프로토타입 빈이 필요할 때
   
2. **지연 초기화(Lazy Initialization)**
   - 무거운 리소스를 사용하는 빈을 실제 사용 시점까지 생성 지연
   
3. **선택적 의존성(Optional Dependencies)**
   - 특정 조건에서만 의존성이 필요한 경우
   
4. **순환 참조 해결**
   - 두 빈이 서로 의존하는 순환 참조 문제 회피

5. **동적 빈 선택**
   - 런타임에 조건에 따라 다른 빈을 선택해야 할 때

### 9.3 DL 구현 방식의 진화

1. **ApplicationContext 직접 사용** (초기 방식)
   ```java
   @Autowired
   private ApplicationContext context;
   
   public void doSomething() {
       PrototypeBean bean = context.getBean(PrototypeBean.class);
       bean.process();
   }
   ```
   - **문제점**: 컨테이너 전체를 주입받아 무겁고, 테스트하기 어려움

2. **ObjectFactory 도입** (개선)
   ```java
   @Autowired
   private ObjectFactory<PrototypeBean> beanFactory;
   
   public void doSomething() {
       PrototypeBean bean = beanFactory.getObject();
       bean.process();
   }
   ```
   - **특징**: 단일 빈 타입만 조회하는 기능으로 한정

3. **ObjectProvider 확장** (현재 권장)
   ```java
   @Autowired
   private ObjectProvider<PrototypeBean> beanProvider;
   
   public void doSomething() {
       PrototypeBean bean = beanProvider.getObject();
       bean.process();
   }
   ```
   - **특징**: 스트림 지원, 옵셔널 처리 등 편의 기능 추가

4. **JSR-330 Provider** (표준)
   ```java
   @Autowired
   private Provider<PrototypeBean> provider;
   
   public void doSomething() {
       PrototypeBean bean = provider.get();
       bean.process();
   }
   ```
   - **특징**: 자바 표준 API로 프레임워크 독립적

## 10. Provider의 내부 동작 원리

### 10.1 ObjectProvider의 동작 메커니즘

- **기본 구조**
  ObjectProvider는 기본적으로 ObjectFactory를 상속한 인터페이스입니다.
  
  ```java
  public interface ObjectProvider<T> extends ObjectFactory<T>, Iterable<T> {
      // 추가 메서드들...
  }
  ```

- **핵심 동작 원리**
  1. ObjectProvider는 빈을 직접 가지고 있지 않고, 빈을 찾아올 수 있는 **위임 로직**만 포함
  2. getObject() 호출 시 내부적으로 **BeanFactory**에 위임하여 요청한 빈을 찾음
  3. 스프링 컨테이너의 DefaultListableBeanFactory 클래스에서 실제 빈을 조회

  ```java
  // 내부 구현 예시 (실제 코드 간소화)
  public class DefaultObjectProvider<T> implements ObjectProvider<T> {
      private final BeanFactory beanFactory;
      private final String beanName;
      private final Class<T> type;
      
      @Override
      public T getObject() {
          // 실제 빈 팩토리에 빈 조회 요청을 위임
          return beanFactory.getBean(beanName, type);
      }
  }
  ```

- **프로토타입 빈 처리**
  ObjectProvider가 프로토타입 빈을 요청할 때 컨테이너는:
  1. 요청 시점에 해당 빈의 **새 인스턴스를 생성**
  2. 의존관계 주입 및 초기화 메서드 실행
  3. 생성된 새 인스턴스를 반환
  4. 각 getObject() 호출마다 위 과정 반복

- **스코프 인식 기능**
  - ObjectProvider는 스코프를 인식하여 각 스코프에 맞는 빈 인스턴스 제공
  - 웹 request 스코프인 경우 현재 HTTP 요청에 맞는 빈 인스턴스 반환

### 10.2 JSR-330 Provider의 동작 원리

- **자바 표준 API 설계**
  - javax.inject 패키지(또는 jakarta.inject)의 Provider 인터페이스는 단순한 구조로 설계됨
  
  ```java
  public interface Provider<T> {
      T get();
  }
  ```

- **스프링에서의 구현**
  1. 스프링은 내부적으로 JSR-330 Provider를 위한 어댑터 클래스를 제공
  2. 이 어댑터는 실제 스프링의 BeanFactory를 통해 빈을 찾는 로직을 구현
  
  ```java
  // 내부 구현 예시 (실제 코드 간소화)
  class ProviderAdapter<T> implements Provider<T> {
      private final BeanFactory beanFactory;
      private final String beanName;
      private final Class<T> type;
      
      @Override
      public T get() {
          // 스프링 컨테이너에 빈 조회를 위임
          return beanFactory.getBean(beanName, type);
      }
  }
  ```

- **생명주기와 스코프 관리**
  - Provider.get() 호출 시 스프링은 해당 빈의 스코프에 맞게 인스턴스 관리
  - 프로토타입 빈의 경우 매번 새로운 인스턴스 생성
  - 웹 스코프 빈의 경우 해당 스코프에 맞는 인스턴스 제공

### 10.3 Provider의 확장 기능

#### ObjectProvider의 확장 기능

```java
// 옵셔널 처리
PrototypeBean bean = beanProvider.getIfAvailable(() -> new PrototypeBean());

// 스트림 처리
beanProvider.stream()
    .filter(bean -> bean.isEnabled())
    .forEach(bean -> bean.process());

// 조건부 처리
beanProvider.ifAvailable(bean -> bean.process());
```

#### 실제 사용 예시 - 전략 패턴 구현

```java
@Service
public class PaymentService {
    private final ObjectProvider<List<PaymentStrategy>> strategiesProvider;
    
    public PaymentService(ObjectProvider<List<PaymentStrategy>> strategiesProvider) {
        this.strategiesProvider = strategiesProvider;
    }
    
    public void processPayment(String type, int amount) {
        // 필요한 시점에 모든 전략을 가져와서 적합한 것을 선택
        PaymentStrategy strategy = strategiesProvider.getObject().stream()
            .filter(s -> s.supports(type))
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("지원하지 않는 결제 방식"));
            
        strategy.pay(amount);
    }
}
```

## 11. Provider 활용 고급 패턴

### 11.1 팩토리 패턴 구현

Provider를 사용하여 빈 팩토리 패턴을 간결하게 구현할 수 있습니다.

```java
@Component
public class PrototypeBeanFactory {
    private final Provider<PrototypeBean> prototypeBeanProvider;
    
    public PrototypeBeanFactory(Provider<PrototypeBean> prototypeBeanProvider) {
        this.prototypeBeanProvider = prototypeBeanProvider;
    }
    
    public PrototypeBean createBean(String data) {
        PrototypeBean bean = prototypeBeanProvider.get();
        bean.setData(data);
        return bean;
    }
}
```

### 11.2 지연 초기화 패턴

무거운 빈의 초기화를 실제 사용 시점까지 지연시키는 패턴입니다.

```java
@Service
public class HeavyResourceService {
    private final Provider<ExpensiveResource> resourceProvider;
    private ExpensiveResource cachedResource;
    
    public HeavyResourceService(Provider<ExpensiveResource> resourceProvider) {
        this.resourceProvider = resourceProvider;
    }
    
    public void processData() {
        // 필요한 시점에 한 번만 초기화
        if (cachedResource == null) {
            cachedResource = resourceProvider.get();
        }
        cachedResource.process();
    }
}
```

### 11.3 조건부 의존성 패턴

특정 조건에 따라 다른 구현체를 사용하는 패턴입니다.

```java
@Service
public class ConfigurableService {
    private final Provider<List<MessageSender>> senderProvider;
    private final Environment env;
    
    public ConfigurableService(Provider<List<MessageSender>> senderProvider, Environment env) {
        this.senderProvider = senderProvider;
        this.env = env;
    }
    
    public void sendMessage(String msg) {
        // 환경에 따라 다른 구현체 선택
        String profile = env.getActiveProfiles()[0];
        MessageSender sender = senderProvider.get().stream()
            .filter(s -> s.supportsProfile(profile))
            .findFirst()
            .orElseGet(() -> senderProvider.get().get(0)); // 기본 구현체
            
        sender.send(msg);
    }
}
```

### 11.4 프록시 패턴과의 결합

Provider를 프록시 패턴과 결합하여 유연한 확장이 가능합니다.

```java
@Component
public class TransactionalPrototypeProxy {
    private final Provider<PrototypeBean> beanProvider;
    private final TransactionManager txManager;
    
    public TransactionalPrototypeProxy(Provider<PrototypeBean> beanProvider, 
                                      TransactionManager txManager) {
        this.beanProvider = beanProvider;
        this.txManager = txManager;
    }
    
    public void executeWithTx() {
        // 매번 새로운 빈을 트랜잭션 컨텍스트에서 실행
        txManager.begin();
        try {
            PrototypeBean bean = beanProvider.get();
            bean.execute();
            txManager.commit();
        } catch (Exception e) {
            txManager.rollback();
            throw e;
        }
    }
}
``` 