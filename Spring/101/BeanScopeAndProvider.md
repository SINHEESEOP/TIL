# TIL: 스프링 빈 스코프와 Provider의 이해와 활용 🔄

> 스프링 빈이 존재할 수 있는 범위(스코프)에 대한 개념과 종류, 그리고 프로토타입 스코프를 활용할 때 발생하는 문제점과 해결책을 정리했습니다.

## 1. 빈 스코프란? 📌

- **정의** 💡  
  스프링 빈이 생성되고 존재할 수 있는 범위를 의미합니다.

- **기본 스코프** ⚡  
  기본적으로 모든 스프링 빈은 **싱글톤 스코프**로 생성됩니다.

## 2. 스프링이 지원하는 스코프 종류 🗂️

- **싱글톤(Singleton)** 🏠  
  - 스프링 컨테이너 시작과 종료까지 유지되는 가장 넓은 범위
  - 기본 스코프로 별도 설정 없이 사용됨

- **프로토타입(Prototype)** 🧬  
  - 스프링 컨테이너는 빈 생성과 의존관계 주입까지만 관여
  - 이후 관리는 클라이언트에 위임하는 매우 짧은 범위

- **웹 관련 스코프** 🌐  
  - **request**: 웹 요청이 들어오고 나갈 때까지 유지
  - **session**: 웹 세션이 생성되고 종료될 때까지 유지
  - **application**: 웹의 서블릿 컨텍스트와 같은 범위로 유지

## 3. 빈 스코프 지정 방법 📋

- **컴포넌트 스캔으로 자동 등록** 🚀

```java
@Scope("prototype")
@Component
public class HelloBean {}
```

- **설정 클래스에서 수동 등록** ⚙️

```java
@Scope("prototype")
@Bean
PrototypeBean helloBean() {
    return new HelloBean();
}
```

## 4. 싱글톤 스코프와 프로토타입 스코프 비교 ⚖️

### 싱글톤 스코프 특징 🏆

- **생명주기** 🕰️  
  - 스프링 컨테이너 시작 시 생성
  - 스프링 컨테이너 종료 시까지 유지

- **인스턴스** 👤  
  - 항상 같은 인스턴스를 반환
  - 상태를 공유하므로 stateless 설계 필요

- **동작 과정** 🔄  
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

### 프로토타입 스코프 특징 🔄

- **생명주기** 🕰️  
  - 스프링 컨테이너에서 빈 조회 시점에 생성
  - 생성, 의존관계 주입, 초기화까지만 스프링이 관여
  - 이후 관리는 클라이언트에 위임

- **인스턴스** 👥  
  - 조회할 때마다 항상 새로운 인스턴스 생성
  - 상태를 공유하지 않음

- **동작 과정** 🔄  
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

## 5. 프로토타입 스코프의 문제점 ⚠️

### 싱글톤 빈과 함께 사용 시 문제 ⚡

- **의도치 않은 동작** 🔍  
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

- **원인** 📝  
  - 싱글톤 빈은 생성 시점에만 의존관계 주입이 일어남
  - 프로토타입 빈은 주입 시점에 새로 생성되지만, 이후에는 새로 생성되지 않음
  - 결과적으로 싱글톤 빈 내부의 프로토타입 빈은 싱글톤처럼 동작

## 6. 프로토타입 스코프 문제 해결책 🛠️

### 1. ApplicationContext 직접 사용 📋

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

- **장점** ✅
  - 매번 새로운 프로토타입 빈 생성 보장
  
- **단점** ❌
  - 스프링 컨테이너에 종속적인 코드
  - 단위 테스트 어려움

### 2. ObjectProvider/ObjectFactory 사용 🔄

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

- **특징** 🔍
  - ObjectFactory: 기본 기능만 제공
  - ObjectProvider: ObjectFactory 상속, 편의 기능 추가
  - 스프링 의존적이지만 기능이 단순해 테스트 용이
  - 별도 라이브러리 필요 없음

### 3. JSR-330 Provider 사용 🧩

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

- **특징** 🔍
  - 자바 표준(JSR-330)
  - 기능이 단순(get() 메서드만 존재)
  - 스프링이 아닌 다른 컨테이너에서도 사용 가능
  - 별도 라이브러리 추가 필요
    - 스프링 부트 3.0 미만: javax.inject:javax.inject:1
    - 스프링 부트 3.0 이상: jakarta.inject:jakarta.inject-api:2.0.1

## 7. 실무 활용 가이드 💼

### 언제 프로토타입 스코프를 사용할까? 🤔

- 매번 사용할 때마다 새로운 객체가 필요한 경우
- 의존관계 주입이 완료된 새로운 객체가 필요한 경우

### 실무에서는? 💻

- 싱글톤으로 대부분의 문제 해결 가능
- 프로토타입 빈을 직접 사용하는 경우는 매우 드묾
- Provider는 DL이 필요한 모든 경우에 사용 가능

### 선택 가이드 🧭

- 스프링 의존적 코드를 작성해도 괜찮다면
  → ObjectProvider (편의성 높음)
- 다른 컨테이너에서도 코드를 사용해야 한다면
  → JSR-330 Provider (자바 표준)
- 별도 기능이 필요 없고 스프링에 의존해도 된다면
  → 스프링이 제공하는 기능 사용

## 8. 핵심 포인트 요약 📚

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