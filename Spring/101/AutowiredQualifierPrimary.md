# TIL: Spring 의존성 주입 시 빈 선택 전략 🔍

> Spring에서 같은 타입의 빈이 여러 개 있을 때 의존성 주입 방법을 정리했습니다.

## 1. 문제 상황: 조회 대상 빈이 2개 이상일 때 ⚠️

- **배경** 📝  
  - `@Autowired`는 기본적으로 타입으로 빈을 찾음
  - 같은 타입의 빈이 2개 이상 등록되어 있으면 `NoUniqueBeanDefinitionException` 발생
  - 예: `DiscountPolicy` 인터페이스의 구현체 `RateDiscountPolicy`와 `FixDiscountPolicy`가 모두 빈으로 등록된 경우

## 2. 해결 방법 1: @Autowired 필드 명 매칭 🏷️

- **작동 방식** 💡  
  - `@Autowired`는 **타입 매칭을 시도**하고, 여러 빈이 있으면 **필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭**

- **적용 예시** 📑

```java
// 기존 코드 - 어떤 구현체를 주입할지 모호함
@Autowired
private DiscountPolicy discountPolicy;

// 해결 - 필드명을 빈 이름으로 변경
@Autowired
private DiscountPolicy rateDiscountPolicy;
```

- **매칭 순서** 🔄  
  1. 타입 매칭
  2. 타입 매칭의 결과가 2개 이상일 때 필드명, 파라미터명으로 빈 이름 매칭

## 3. 해결 방법 2: @Qualifier 사용 🎯

- **개념** 💭  
  - 추가 구분자를 붙여주는 방법 (빈 이름 변경 X)
  - 주입시 추가적인 방법을 제공하는 것

- **적용 방법** 📑

```java
// 빈 등록 시 @Qualifier 지정
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier("fixDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy {}

// 생성자 주입 시
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
                        @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}

// 수정자 주입 시
@Autowired
public void setDiscountPolicy(@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
}
```

- **직접 빈 등록 시에도 사용 가능** 📑

```java
@Bean
@Qualifier("mainDiscountPolicy")
public DiscountPolicy discountPolicy() {
    return new RateDiscountPolicy();
}
```

- **매칭 순서** 🔄  
  1. `@Qualifier`끼리 매칭
  2. 빈 이름 매칭
  3. `NoSuchBeanDefinitionException` 예외 발생

## 4. 해결 방법 3: @Primary 사용 ⭐

- **개념** 💭  
  - 우선순위를 정하는 방법
  - `@Autowired` 시에 여러 빈이 매칭되면 `@Primary`가 지정된 빈이 우선권을 가짐

- **적용 방법** 📑

```java
// Primary 지정
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}

// 사용 코드 - 특별한 지정 없이 주입
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
                        DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

## 5. @Primary와 @Qualifier 활용 전략 💼

- **실무 예시** 📊  
  - **메인 데이터베이스 커넥션 빈**: `@Primary` 적용 → 대부분의 상황에서 편리하게 사용
  - **서브 데이터베이스 커넥션 빈**: `@Qualifier` 지정 → 특별한 상황에서만 명시적으로 사용

- **장단점 비교** ⚖️  
  - `@Qualifier`의 단점: 모든 주입 코드에 `@Qualifier` 지정 필요
  - `@Primary`의 장점: 자주 사용하는 빈에 적용하면 코드를 깔끔하게 유지 가능

## 6. 우선순위 ⭐⭐⭐

- **원칙** 💡  
  - **스프링은 자동보다는 수동이, 넓은 범위의 선택권보다는 좁은 범위의 선택권이 우선순위가 높음**
  - `@Qualifier`가 `@Primary`보다 우선순위가 높음
  - 즉, `@Primary`는 기본값처럼 동작하고, `@Qualifier`는 상세하게 동작

## 학습 포인트 ✨

- **다양한 선택 방법**: 필드명 매칭, `@Qualifier`, `@Primary` 등 상황에 맞는 전략 선택
- **우선순위 이해**: 좁은 범위의 선택권이 넓은 범위의 선택권보다 우선
- **실무 적용**: 자주 사용하는 빈은 `@Primary`, 특별한 경우 `@Qualifier` 조합으로 코드 깔끔하게 유지 