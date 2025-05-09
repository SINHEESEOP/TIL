# TIL: 자동 주입과 수동 빈 등록의 실무 운영 기준 🔄

## 핵심 요약 💎

- **기본 원칙**: 편리한 자동 기능을 기본으로 사용
- **수동 등록 권장 상황**:
  - 기술 지원 객체(공통 기술)는 수동 등록
  - 다형성을 적극 활용하는 비즈니스 로직은 수동 등록 고려
  - 광범위한 영향을 미치는 컴포넌트는 명확하게 드러내기 위해 수동 등록

## 자동 vs 수동 등록 선택 기준 🎯

| 구분 | 자동 등록 | 수동 등록 |
|------|---------|----------|
| **업무 로직** | 👍 권장 (컨트롤러, 서비스, 리포지토리) | 다형성 활용 시 고려 |
| **기술 지원** | ⚠️ 비권장 | 👍 권장 (AOP, DB 연결 등) |
| **복잡도** | 낮음 | 높음 |
| **유지보수성** | 분산됨 | 집중됨 |

## 상세 내용

### 1. 자동 주입의 발전과 현재 추세 📈

- **진화 과정** 🕰️  
  - 스프링 프레임워크 초기: XML 기반 수동 설정 → 자바 코드 기반 설정 → 자동 스캔
  - 현재 추세: 자동 주입 선호 (특히 스프링 부트에서)

- **자동 주입의 장점** 💡  
  - `@Component`, `@Controller`, `@Service`, `@Repository` 등 계층별 자동 등록 지원
  - 개발 생산성 향상 및 설정 코드 감소
  - 설정 정보 관리 부담 감소

### 2. 업무 로직 vs 기술 지원 로직 🔍

- **업무 로직** 📊  
  - 정의: 웹 컨트롤러, 비즈니스 서비스, 데이터 리포지토리 등
  - 특징: 수가 많고 유사한 패턴을 가짐
  - 문제 발생 시: 원인 파악이 비교적 용이함
  - 권장: **자동 주입 방식**

- **기술 지원 로직** ⚙️  
  - 정의: 공통 관심사(AOP), DB 연결, 로깅 등 하부 기술
  - 특징: 수가 적고 애플리케이션 전반에 영향을 미침
  - 문제 발생 시: 원인 파악이 어려움
  - 권장: **수동 빈 등록**으로 명확하게 드러내기

### 3. 다형성을 활용하는 비즈니스 로직의 경우 🧩

- **문제 상황** ⚠️  
  - `Map<String, DiscountPolicy>`와 같이 여러 빈이 주입되는 경우
  - 코드만 보고 어떤 빈이 주입될지 파악하기 어려움
  - 다른 개발자가 코드를 이해하기 어려울 수 있음

- **해결 방안** 🛠️  
  - 별도의 설정 정보를 만들어 수동으로 등록

```java
@Configuration
public class DiscountPolicyConfig {
    @Bean
    public DiscountPolicy rateDiscountPolicy() {
        return new RateDiscountPolicy();
    }
    
    @Bean
    public DiscountPolicy fixDiscountPolicy() {
        return new FixDiscountPolicy();
    }
}
```

- **대안** 🔄  
  - 자동 등록을 사용하더라도 관련 구현체를 특정 패키지에 모아서 관리
  - 목적은 코드의 의도를 명확하게 드러내는 것

### 4. 예외 사항 및 특수 케이스 🌟

- **스프링/스프링 부트 자동 등록 빈** 🔄  
  - 스프링 부트가 자동으로 등록하는 빈들(DataSource 등)은 예외
  - 스프링의 의도대로 사용하는 것이 중요
  - 메뉴얼을 참고하여 편리하게 사용

- **직접 만든 기술 지원 객체** 🔧  
  - 직접 개발한 기술 지원 객체는 수동 등록 권장
  - 설정 정보에 명확하게 드러내는 것이 유지보수에 유리

## 적용 방법 및 예시 📝

- **자동 등록 기본 사용** 🚀  
  - 대부분의 컨트롤러, 서비스, 리포지토리에 `@Component` 및 관련 어노테이션 사용
  - 컴포넌트 스캔을 통한 자동 등록

- **수동 등록이 필요한 경우** 📌  
  - 트랜잭션 관리, 보안, 모니터링 등 기술 지원 로직
  - 한 인터페이스에 여러 구현체가 있어 명확한 관리가 필요한 경우

## 학습 포인트 📚

- 자동 주입과 수동 등록의 장단점 이해
- 상황에 맞는 적절한 빈 등록 방식 선택
- OCP, DIP 원칙을 지키면서도 개발 편의성 확보
- 코드의 의도를 명확하게 드러내는 설계 방식 채택
- 팀 내 일관된 규칙 수립의 중요성 