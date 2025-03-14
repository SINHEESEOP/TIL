# 🔄 AOP (Aspect Oriented Programming)

## 📌 AOP란 무엇인가?

AOP(Aspect Oriented Programming)는 **관점 지향 프로그래밍**으로, 공통 관심사(Cross-Cutting Concern)를 분리하여 모듈성을 높이는 프로그래밍 패러다임입니다. 스프링 프레임워크의 핵심 기능 중 하나로, 로깅, 트랜잭션 관리, 보안과 같은 기능을 비즈니스 로직과 분리할 수 있습니다.

## 🚀 AOP가 필요한 상황

- 모든 메소드의 실행 시간을 측정하고 싶은 경우
- 회원 가입 시간, 회원 조회 시간 등 핵심 로직 외에 **공통 관심 사항**(Cross-Cutting Concern)을 간단히 추가/관리하고 싶은 경우
- 공통 로직이 여러 곳에 흩어지면 유지보수가 어렵고, 수정해야 할 때마다 전부 수정해야 하는 단점이 있음

즉, '시간 측정'과 같은 공통 로직을 핵심 로직과 분리해 관리할 필요가 있을 때 AOP가 유용합니다.

## ⚠️ AOP를 적용하지 않았을 때의 문제점

예를 들어, `MemberService`에 회원가입(`join`), 회원 조회(`findMembers`) 기능 각각에 시간을 측정하는 코드를 넣으면 다음과 같은 문제가 발생합니다:

```java
// 💡 시간 측정 로직이 서비스 코드 내에 흩어져 있는 예시
public Long join(Member member) {
    long start = System.currentTimeMillis(); // 시간 측정 시작
    try {
        validateDuplicateMember(member); // 핵심 비즈니스 로직
        memberRepository.save(member);   // 핵심 비즈니스 로직
        return member.getId();
    } finally {
        long finish = System.currentTimeMillis();
        long timeMs = finish - start;
        System.out.println("join " + timeMs + "ms"); // 시간 측정 결과 출력
    }
}

public List<Member> findMembers() {
    long start = System.currentTimeMillis(); // 시간 측정 시작 - 중복 코드
    try {
        return memberRepository.findAll(); // 핵심 비즈니스 로직
    } finally {
        long finish = System.currentTimeMillis();
        long timeMs = finish - start;
        System.out.println("findMembers " + timeMs + "ms"); // 시간 측정 결과 출력 - 중복 코드
    }
}
```

### 🔴 주요 문제점:
- **핵심 로직**(회원 로직)과 **공통 로직**(시간 측정)이 섞여 코드의 복잡도가 높아집니다.
- 공통 로직(시간 측정)을 변경하려면 이 로직이 들어간 모든 서비스를 수정해야 합니다.
- 전체 소스의 가독성과 유지보수성이 떨어집니다.

## 🛠️ AOP 적용 방법

스프링에서 AOP를 구현할 때는 크게 다음 두 단계를 거칩니다:

1. 공통 로직(Advice)을 분리해 `@Aspect` 클래스에 모아둡니다.  
2. 어떤 시점(Around, Before, After 등)에서 어떤 대상에 적용할지(Pointcut)를 결정합니다.

### ✅ 예시: 시간 측정 AOP 구현

```java
// 💡 AOP를 사용한 시간 측정 로직 구현 예시
@Component  // 스프링 빈으로 등록
@Aspect     // AOP 클래스로 지정
public class TimeTraceAop {

    // 어떤 메소드에 이 AOP를 적용할지 지정 (Pointcut)
    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("▶️ START: " + joinPoint.toString());
        
        try {
            // 핵심 로직(메서드) 실행
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("⏹️ END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }
}
```

### 🔍 주요 개념 설명:
- `@Around("execution(* hello.hellospring..*(..))")`  
  - `hello.hellospring` 패키지와 그 하위 패키지에 있는 모든 메소드에 시간 측정 로직을 적용합니다.
  - `*` - 모든 반환 타입
  - `..` - 모든 하위 패키지
  - `(..)` - 모든 파라미터
- `joinPoint.proceed()`를 호출하면 대상 메소드가 실제로 실행됩니다.

## 🌟 AOP 적용 후의 이점

- 시간 측정 같은 **공통 관심 사항**을 한 곳에서 모아서 관리할 수 있습니다.
- **핵심 관심 사항**(회원가입, 회원 조회 등 비즈니스 로직)을 깔끔하게 유지할 수 있습니다.
- 수정이 필요할 경우 AOP 로직만 수정하면 되므로 유지보수가 용이해집니다.
- 원하는 적용 대상을 Pointcut으로 유연하게 선택 가능합니다.

### 🔄 적용 전과 후 비교:

#### 적용 전 (MemberService):
```java
// 비즈니스 로직과 시간 측정 로직이 혼합됨
public Long join(Member member) {
    // 시간 측정 코드 (관심사가 섞임)
    long start = System.currentTimeMillis();
    try {
        // 실제 비즈니스 로직
        validateDuplicateMember(member);
        memberRepository.save(member);
        return member.getId();
    } finally {
        // 시간 측정 코드 (관심사가 섞임)
        long finish = System.currentTimeMillis();
        long timeMs = finish - start;
        System.out.println("join " + timeMs + "ms");
    }
}
```

#### 적용 후 (MemberService):
```java
// 순수한 비즈니스 로직만 남음
public Long join(Member member) {
    validateDuplicateMember(member);
    memberRepository.save(member);
    return member.getId();
}
```

## 📝 마무리 및 요약

AOP를 통해 비즈니스 로직과 반복적으로 필요한 공통 로직을 분리함으로써 코드의 유지보수성을 높일 수 있습니다. 특히, 공통 로직이 점점 복잡해질수록 AOP의 중요성이 크게 부각됩니다.

### 🔑 핵심 포인트:
- AOP로 '시간 측정'과 같은 공통 로직을 분리하여 핵심 로직을 깔끔하게 유지
- `@Aspect`, `@Around` 등 스프링 AOP 애노테이션 활용
- 필요한 메소드만 선택적으로 공통 로직을 적용 가능
- 코드의 모듈성과 재사용성 향상

### 📚 관련 참고 자료:
- [Spring 공식 문서 - AOP](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop)
- [Baeldung - Introduction to Spring AOP](https://www.baeldung.com/spring-aop)
