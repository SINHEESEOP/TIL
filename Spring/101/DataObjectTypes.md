# 데이터 객체 타입: DTO, Entity, VO, DAO 완벽 정리 🧩

DTO, 도메인 객체, VO, DAO는 모두 소프트웨어 설계에서 데이터를 표현하고 처리하기 위해 사용되는 개념들입니다. 이들의 역할과 차이점을 쉽게 설명해보겠습니다.

## 1. DTO (Data Transfer Object) 📦

- **목적:** 데이터를 전송하기 위한 객체
- **주요 특징:**
    - 클라이언트와 서버 간, 또는 계층 간 데이터를 주고받을 때 사용
    - 비즈니스 로직이 없는 순수한 데이터 구조
    - 보통 getter와 setter만 포함하며, 데이터를 담고 전송하는 데 초점
- **예시:**
    
    ```java
    public class UserDTO {
        private String name;
        private int age;
    
        public UserDTO(String name, int age) {
            this.name = name;
            this.age = age;
        }
    
        // Getter, Setter
    }
    ```
    
- **사용 사례:**
    - 클라이언트에서 서버로 데이터를 보낼 때
    - 컨트롤러와 서비스 계층 간 데이터를 교환할 때

## 2. Entity (도메인 객체) 🏛️

- **목적:** 비즈니스 로직과 데이터 표현의 핵심 객체
- **주요 특징:**
    - 도메인(비즈니스 요구사항)을 모델링한 객체
    - 비즈니스 로직을 포함하고, 애플리케이션에서 데이터를 처리하는 데 중심 역할
    - 엔티티(Entity)와 혼용해서 사용되는 경우가 많음
- **예시:**
    
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
    }
    ```
    
- **사용 사례:**
    - 도메인(비즈니스) 모델을 표현
    - 서비스 계층에서 비즈니스 로직을 처리할 때 사용

## 3. VO (Value Object) 💎

- **목적:** 변하지 않는 값(불변 객체)을 표현
- **주요 특징:**
    - 값 자체를 비교하는 객체(내용으로 동등성 판단)
    - **불변성**(Immutable)을 가지며, 생성 후 상태가 변하지 않음
    - 데이터의 일부 속성을 캡슐화하여 의미를 부여
- **예시:**
    
    ```java
    public class Money {
        private final int amount;
        private final String currency;
    
        public Money(int amount, String currency) {
            this.amount = amount;
            this.currency = currency;
        }
    
        // 값 비교를 위해 equals와 hashCode 구현
        @Override
        public boolean equals(Object obj) {
            if (this == obj) return true;
            if (!(obj instanceof Money)) return false;
            Money other = (Money) obj;
            return this.amount == other.amount && this.currency.equals(other.currency);
        }
    
        @Override
        public int hashCode() {
            return Objects.hash(amount, currency);
        }
    }
    ```
    
- **사용 사례:**
    - 금액, 날짜, 좌표와 같은 **값 그 자체**를 나타낼 때

## 4. DAO (Data Access Object) 🗄️

- **목적:** 데이터베이스와의 접근을 담당하는 객체
- **주요 특징:**
    - 데이터베이스와 애플리케이션 간의 연결 고리 역할
    - 데이터의 CRUD(Create, Read, Update, Delete) 작업을 수행
    - SQL 쿼리나 ORM(EntityManager, Repository 등)을 통해 데이터를 처리
- **예시:**
    
    ```java
    public class UserDAO {
        public User findById(int id) {
            // 데이터베이스에서 사용자 검색
            return new User("John Doe", 25);
        }
    
        public void save(User user) {
            // 데이터베이스에 사용자 저장
        }
    }
    ```
    
- **사용 사례:**
    - 데이터베이스에서 데이터를 읽거나 쓰는 작업을 담당
    - 서비스 계층에서 호출하여 데이터베이스 작업 수행

## 차이점 정리 📊

| **구분** | **DTO** | **Entity** | **VO** | **DAO** |
| --- | --- | --- | --- | --- |
| **목적** | 데이터 전송 | 비즈니스 로직 표현 | 변하지 않는 값 표현 | 데이터베이스 작업 |
| **포함 기능** | Getter/Setter | 비즈니스 로직 포함 | 불변성 유지, 값 동등성 비교 | CRUD 작업(SQL 처리 등) |
| **가변성** | 가변 | 가변 | 불변 | 가변 |
| **사용 위치** | 계층 간 데이터 교환 | 서비스 계층, 도메인 계층 | 값 자체 표현 | 데이터 계층 |
| **예시 사용 대상** | 클라이언트와 서버 간 통신 | 사용자, 주문 등 도메인 객체 | 금액, 좌표 등 | DB 접근을 위한 객체 |

## 요약 및 비유 🎯

1. **DTO**: 데이터를 계층 간 이동시키기 위한 단순한 데이터 객체
2. **Entity**: 비즈니스 로직을 포함한 애플리케이션의 핵심 모델
3. **VO**: 불변 객체로, 값 그 자체를 표현
4. **DAO**: 데이터베이스와의 통신을 담당하는 객체

쉽게 비유하자면:
- **DTO는 데이터를 전달하는 택배 상자** 📦
- **Entity는 애플리케이션의 심장** ❤️
- **VO는 변하지 않는 값의 표현** 💎
- **DAO는 DB 작업을 담당하는 직책** 🔑 