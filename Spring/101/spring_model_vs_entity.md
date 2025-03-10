# 🔄 Spring에서의 Model과 Entity 비교

Model과 Entity는 이름은 비슷해 보이지만 Spring 애플리케이션에서 서로 다른 목적과 역할을 가집니다. 이 문서에서는 두 개념의 차이점과 관계를 명확히 정리합니다.

## 💡 핵심 요약

**Model과 Entity의 관계:**
- MVC(Model-View-Controller) 패턴에서 **Model**은 View, Controller를 제외한 모든 것
- **Entity**는 Model의 특별한 형태로, 데이터베이스 테이블과 매핑되는 객체
- 즉, **Entity, DTO, VO, Service 로직** 등이 모두 Model의 일부로 포함됨

## 1. Model이란? 📊

**정의**: Model은 **애플리케이션 내 데이터와 비즈니스 로직을 표현**하는 객체입니다.

**주요 특징:**
1. **비즈니스 로직 포함:**
   - 데이터를 처리, 검증, 가공하는 비즈니스 로직이 포함됨
   - 복잡한 연산이나 규칙을 구현할 수 있음

2. **데이터베이스와 독립적:**
   - 반드시 데이터베이스와 매핑될 필요 없음
   - 애플리케이션의 상태나 데이터를 표현하는데 중점

3. **넓은 의미:**
   - MVC 패턴에서 Model은 비즈니스 로직과 데이터를 모두 포함하는 광범위한 개념
   - View에 표시될 데이터를 준비하고 관리

### Model 예시 코드

```java
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 비즈니스 로직 예시
    public boolean isAdult() {
        return this.age >= 18;
    }

    // Getter, Setter 메서드
}
```

여기서 `isAdult()`는 비즈니스 로직의 일부로, 데이터베이스와 관계없이 애플리케이션에서 사용됩니다.

## 2. Entity란? 📁

**정의**: Entity는 **데이터베이스 테이블과 직접적으로 매핑되는 객체**로, 주로 ORM(Object-Relational Mapping) 프레임워크에서 사용됩니다.

**주요 특징:**
1. **데이터베이스와 직접 매핑:**
   - ORM(JPA, Hibernate 등)에서 테이블의 컬럼과 객체의 속성을 매핑
   - 데이터베이스 스키마와 밀접하게 연결됨

2. **영속성(Persistence):**
   - Entity는 영속성 컨텍스트에서 관리됨 
   - JPA에서는 `@Entity` 어노테이션으로 표시
   - 생명주기가 데이터베이스의 레코드와 동기화됨

3. **비즈니스 로직 최소화:**
   - Entity는 주로 데이터 구조와 관계에 집중
   - 복잡한 비즈니스 로직은 보통 서비스 계층으로 분리

### Entity 예시 코드 (JPA)

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    private int age;

    // JPA는 기본 생성자 필요
    protected User() {}

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getter, Setter 메서드
}
```

이 클래스는 데이터베이스의 `users` 테이블과 매핑되며, JPA를 통해 CRUD 작업이 수행됩니다.

## 3. Model과 Entity의 차이점 📋

| **구분** | **Model** | **Entity** |
|---------|----------|------------|
| **목적** | 애플리케이션의 데이터 및 비즈니스 로직 표현 | 데이터베이스 테이블과 직접 매핑 |
| **DB와의 관계** | 데이터베이스와 독립적 가능 | 데이터베이스 테이블과 1:1 매핑 |
| **사용 위치** | 서비스 계층, 도메인 로직, 뷰 데이터 준비 | 데이터 액세스 계층, JPA/Hibernate |
| **로직 포함** | 다양한 비즈니스 로직 포함 가능 | 주로 데이터 구조와 관계에 집중 |
| **영속성** | 영속성과 직접 관련 없음 | 영속성 컨텍스트에 의해 관리 |
| **어노테이션** | 특별한 어노테이션 불필요 | `@Entity`, `@Table`, `@Id` 등 사용 |

## 4. Model과 Entity의 관계 🔄

- **Entity는 Model의 한 종류**라고 볼 수 있습니다:
  - Entity는 데이터베이스와 연결된 특수한 형태의 Model
  - 모든 Entity는 Model이지만, 모든 Model이 Entity인 것은 아님
  - DTO, VO도 Model의 일종이지만 Entity와는 다른 목적을 가짐

## 5. 실무적 관점에서의 비유 🚀

1. **Model**:
   - 애플리케이션의 데이터와 행동을 담는 컨테이너
   - 여러 층의 데이터 표현과 비즈니스 로직을 포함하는 넓은 개념
   - 예: "자동차의 설계도와 기능 명세서"

2. **Entity**:
   - 데이터베이스에 저장될 구체적인 데이터 구조
   - 데이터의 지속성과 관계에 중점을 둔 객체
   - 예: "데이터베이스 테이블의 실제 제품 정보"

## 6. 사용 사례로 이해하기 📱

**Entity 중심 작업:**
- 데이터베이스에서 정보 조회 및 저장
- 테이블 간 관계 매핑 (1:N, M:N 등)
- JPA/Hibernate를 통한 ORM 작업

**Model 중심 작업:**
- 화면에 표시할 데이터 준비
- 사용자 입력 검증 및 처리
- 여러 Entity를 조합하여 복잡한 비즈니스 로직 수행
- API 응답 데이터 구성

## 7. 결론 ✨

- **Model**은 애플리케이션의 데이터와 비즈니스 로직을 담당하는 광범위한 개념
- **Entity**는 데이터베이스 테이블과 매핑되는 특수한 유형의 Model
- 실무에서는 Entity를 직접 View로 전달하기보다 DTO로 변환하여 사용하는 것이 일반적
- Model과 Entity를 명확히 구분하면 계층 간 책임을 분리하고 유지보수성을 높일 수 있음