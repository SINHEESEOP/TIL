# TIL: Spring에서 커스텀 애노테이션 만들기

> Spring에서 타입 안전한 커스텀 애노테이션을 만들어 코드 품질을 향상시키는 방법을 정리했습니다.

## 1. 커스텀 애노테이션의 필요성

- **기존 문제점**
  - `@Qualifier("mainDiscountPolicy") 같은 문자열 기반 식별자는 컴파일 시 타입 체크가 불가능`
  - `오타가 발생해도 컴파일러가 감지하지 못함`
  - `IDE에서 자동 완성이나 리팩토링 지원이 제한적`

- **해결 방안**
  - 커스텀 애노테이션을 만들어 문자열 대신 타입 안전한 방식으로 의존성 구분
  - 컴파일 타임에 오류 검출 가능
  - 코드 가독성과 유지보수성 향상

## 2. 커스텀 애노테이션 구현 예시

- **메타 애노테이션 활용한 커스텀 애노테이션**

```java
package hello.core.annotation;

import org.springframework.beans.factory.annotation.Qualifier;
import java.lang.annotation.*;

@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER,
        ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}
```

- **각 애노테이션 속성 설명**
  - `@Target`: 애노테이션이 적용될 수 있는 요소 지정
  - `@Retention`: 애노테이션 정보가 유지되는 범위 설정 (RUNTIME은 실행 시점에도 유지)
  - `@Documented`: JavaDoc에 포함
  - `@Qualifier`: 기존 스프링 애노테이션을 메타 애노테이션으로 사용

## 3. 커스텀 애노테이션 활용

- **컴포넌트에 적용**

```java
@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy {
    // 구현 내용
}
```

- **의존성 주입 시 활용**

```java
// 생성자 주입
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
                        @MainDiscountPolicy DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}

// 수정자 주입
@Autowired
public void setDiscountPolicy(@MainDiscountPolicy DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
}
```

## 4. 커스텀 애노테이션의 원리

- **애노테이션 합성**
  - 자바에서 애노테이션은 상속 개념이 없음
  - 대신 여러 애노테이션을 조합해서 새로운 애노테이션을 정의할 수 있음
  - 스프링은 이러한 애노테이션 합성과 조합을 지원

- **다양한 조합 가능성**
  - `@Qualifier` 외에도 다양한 스프링 애노테이션을 재정의 가능
  - `@Autowired`, `@Component` 등도 커스텀 애노테이션으로 재정의 가능

## 5. 사용 시 주의사항

- **무분별한 재정의 지양**
  - 스프링 기본 애노테이션을 뚜렷한 목적 없이 재정의하면 유지보수 혼란 초래
  - 팀 내 컨벤션과 문서화가 중요
  - 명확한 비즈니스 목적이 있는 경우에만 사용 권장

- **버전 호환성 고려**
  - 스프링 버전 업그레이드 시 커스텀 애노테이션 동작 확인 필요
  - 기본 애노테이션 동작이 변경될 경우 영향 받을 수 있음

## 6. 커스텀 애노테이션의 장점

- **타입 안전성**
  - 컴파일 타임에 오류 검출 가능
  - IDE의 자동 완성, 리팩토링 지원

- **코드 가독성**
  - 의도를 명확히 표현하는 이름 사용 가능
  - 자체 문서화 효과

- **유지보수성**
  - 수정 시 한 곳만 변경하면 됨
  - 코드 전체에 일관성 유지 가능

## 학습 포인트

- **스프링 애노테이션 구조**: 스프링의 애노테이션이 어떻게 설계되었는지 이해할 수 있음
- **메타 애노테이션**: 기존 애노테이션을 활용해 새로운 애노테이션을 만드는 패턴
- **타입 안전성**: 문자열 기반 식별자보다 타입 안전한 방식으로 코드 품질 향상
- **코드 표현력**: 도메인 개념을 애노테이션으로 표현하여 코드의 의도를 명확히 전달 