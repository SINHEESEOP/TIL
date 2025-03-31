# TIL: 여러 빈 주입 받기(List, Map) 🧩

> Spring에서 같은 타입의 모든 빈이 필요할 때 컬렉션을 활용하는 방법을 정리했습니다.

## 1. 여러 빈을 동시에 주입받아야 하는 경우 ⚡

- **상황 설명** 📝  
  - 특정 인터페이스의 모든 구현체를 함께 관리해야 하는 경우
  - 사용자가 여러 전략 중 하나를 선택할 수 있어야 하는 경우
  - 전략 패턴(Strategy Pattern)을 스프링으로 구현할 때

- **실제 사례** 🔍  
  - 다양한 할인 정책(고정 할인, 비율 할인 등) 중 선택 기능
  - 다양한 결제 방법(카드, 현금, 포인트 등) 중 선택 기능
  - 다양한 알림 방법(이메일, SMS, 푸시 등)을 모두 사용해야 하는 경우

## 2. 컬렉션을 활용한 빈 주입 방법 🛠️

- **지원하는 컬렉션 타입** 💡  
  - `List<타입>`: 해당 타입의 모든 빈을 리스트로 주입
  - `Map<String, 타입>`: 빈 이름을 키로, 빈 객체를 값으로 하는 맵 주입
  - `Set<타입>`: 해당 타입의 모든 빈을 Set으로 주입(중복 제거)

- **예시 코드** 📑

```java
@Service
public class DiscountService {
    // 빈 이름을 키로, DiscountPolicy 객체를 값으로 갖는 맵
    private final Map<String, DiscountPolicy> policyMap;
    
    // DiscountPolicy 타입의 모든 빈을 리스트로 주입
    private final List<DiscountPolicy> policies;
    
    // 생성자 주입
    public DiscountService(Map<String, DiscountPolicy> policyMap, 
                          List<DiscountPolicy> policies) {
        this.policyMap = policyMap;
        this.policies = policies;
        // 주입된 빈 확인
        System.out.println("policyMap = " + policyMap);
        System.out.println("policies = " + policies);
    }
    
    // 할인 코드(빈 이름)를 기준으로 할인 정책을 선택하여 적용
    public int discount(Member member, int price, String discountCode) {
        DiscountPolicy discountPolicy = policyMap.get(discountCode);
        return discountPolicy.discount(member, price);
    }
}
```

## 3. 전략 패턴 구현 예시 📊

- **테스트 코드** 📑

```java
@Test
void findAllBean() {
    // 스프링 컨테이너 생성 및 빈 등록
    ApplicationContext ac = new AnnotationConfigApplicationContext(
            AutoAppConfig.class, DiscountService.class);
    
    // 서비스 빈 가져오기
    DiscountService discountService = ac.getBean(DiscountService.class);
    
    // 회원 생성
    Member member = new Member(1L, "userA", Grade.VIP);
    
    // "fixDiscountPolicy" 빈을 사용한 할인 적용
    int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");
    
    // 검증
    assertThat(discountService).isInstanceOf(DiscountService.class);
    assertThat(discountPrice).isEqualTo(1000);
}
```

## 4. 주입 동작 방식 설명 🧩

- **Map<String, DiscountPolicy>** 🔄  
  - 키: 스프링 빈의 이름 (예: "fixDiscountPolicy", "rateDiscountPolicy")
  - 값: 해당 타입의 스프링 빈 객체
  - 예시 출력: `{fixDiscountPolicy=hello.core.discount.FixDiscountPolicy@123, rateDiscountPolicy=hello.core.discount.RateDiscountPolicy@456}`

- **List<DiscountPolicy>** 📋  
  - 해당 타입의 모든 스프링 빈을 담은 리스트
  - 빈 등록 순서대로 주입됨
  - 예시 출력: `[hello.core.discount.FixDiscountPolicy@123, hello.core.discount.RateDiscountPolicy@456]`

- **빈이 없는 경우** ⚠️  
  - 해당 타입의 빈이 존재하지 않으면 빈 컬렉션(empty collection)이나 빈 Map 주입
  - NPE(NullPointerException)가 발생하지 않음

## 5. 스프링 컨테이너 생성과 빈 등록 방법 🏗️

- **컨테이너 생성과 동시에 빈 등록하기** 📑

```java
// 컨테이너 생성 + 클래스를 빈으로 자동 등록
ApplicationContext ac = new AnnotationConfigApplicationContext(
        AutoAppConfig.class, DiscountService.class);
```

- **동작 방식** 🔍  
  1. `new AnnotationConfigApplicationContext()`: 스프링 컨테이너 생성
  2. `AutoAppConfig.class, DiscountService.class`: 파라미터로 넘긴 클래스들을 자동으로 스프링 빈으로 등록
  3. `AutoAppConfig`에 `@ComponentScan`이 있다면 해당 설정에 따라 컴포넌트 스캔 또한 동작

## 6. 활용 사례 💼

- **다양한 구현체 선택 기능** 🎯  
  - 사용자가 선택 가능한 여러 기능(결제 방법, 배송 방법 등)
  - 런타임에 전략을 변경할 수 있는 유연한 설계

- **모든 구현체를 순회하는 기능** 🔄  
  - 모든 구현체에게 동일한 작업 수행 (예: 모든 알림 서비스를 통해 알림 전송)
  - 예시: `policies.forEach(policy -> policy.someMethod());`

- **확장에 유리한 설계** 💡  
  - 새로운 구현체 추가 시 코드 변경 없이 확장 가능 (OCP 원칙)
  - 예: 새로운 할인 정책 추가 시 `DiscountService` 코드 수정 불필요

## 학습 포인트 📚

- **DI 컨테이너의 유연성**: 같은 타입의 여러 빈을 다양한 방식으로 주입 가능
- **전략 패턴 구현 편의성**: 스프링을 활용하면 전략 패턴을 간결하게 구현 가능
- **개방-폐쇄 원칙(OCP)**: 기존 코드 변경 없이 새로운 구현체 추가 가능
- **컬렉션 주입**: Map, List, Set 등을 활용한 다양한 주입 패턴 가능 