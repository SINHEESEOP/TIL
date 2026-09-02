# 자바빈(JavaBean) 프로퍼티 규약

## 자바빈이란?

자바빈(JavaBean)은 자바 플랫폼에서 재사용 가능한 소프트웨어 컴포넌트를 의미합니다. 자바빈은 특정 형태의 디자인 패턴을 따르는 자바 클래스로, 데이터 표현을 목적으로 합니다.

## 자바빈 프로퍼티 규약의 핵심 요소

### 1. 기본 생성자 (Default Constructor)

```java
public class Person {
    // 기본 생성자 - 인자가 없는 생성자가 반드시 있어야 함
    public Person() {
        // 생성자 내용은 비어있을 수 있음
    }
}
```

### 2. 프로퍼티 (Properties)

자바빈의 프로퍼티는 private 필드와 public getter/setter 메서드로 구성됩니다.

```java
public class Person {
    // private 필드 - 직접 접근 불가
    private String name;
    private int age;
    
    // 기본 생성자
    public Person() {
    }
    
    // getter 메서드 - 프로퍼티 값 읽기
    public String getName() {
        return this.name;
    }
    
    // setter 메서드 - 프로퍼티 값 변경
    public void setName(String name) {
        this.name = name;
    }
    
    // age 프로퍼티의 getter
    public int getAge() {
        return this.age;
    }
    
    // age 프로퍼티의 setter
    public void setAge(int age) {
        this.age = age;
    }
}
```

### 3. 직렬화 (Serialization)

자바빈은 Serializable 인터페이스를 구현해야 합니다.

```java
import java.io.Serializable;

public class Person implements Serializable {
    // 직렬화 버전 ID (선택사항이지만 권장됨)
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    
    // 기본 생성자와 getter/setter 메서드...
}
```

## 프로퍼티 타입

### 1. 단순 프로퍼티 (Simple Properties)

위의 예시처럼 단일 값을 가지는 프로퍼티입니다.

### 2. 불린 프로퍼티 (Boolean Properties)

불린 타입의 경우 `is` 접두사를 사용할 수 있습니다.

```java
public class Account {
    private boolean active;
    
    // getter - is 접두사 사용
    public boolean isActive() {
        return active;
    }
    
    // setter - 여전히 set 접두사 사용
    public void setActive(boolean active) {
        this.active = active;
    }
}
```

### 3. 인덱스 프로퍼티 (Indexed Properties)

배열과 같은 여러 값을 가질 수 있는 프로퍼티입니다.

```java
public class Library {
    private String[] books;
    
    // 배열 전체를 가져오는 getter
    public String[] getBooks() {
        return books;
    }
    
    // 배열 전체를 설정하는 setter
    public void setBooks(String[] books) {
        this.books = books;
    }
    
    // 특정 인덱스의 값을 가져오는 getter
    public String getBook(int index) {
        return books[index];
    }
    
    // 특정 인덱스의 값을 설정하는 setter
    public void setBook(int index, String book) {
        books[index] = book;
    }
}
```

## 자바빈 프로퍼티 규약의 이점

1. **캡슐화(Encapsulation)** - 필드를 private으로 선언하고 getter/setter를 통해 접근함으로써 데이터 은닉
2. **표준화(Standardization)** - 일관된 방식으로 객체의 속성에 접근
3. **도구 지원(Tool Support)** - IDE와 같은 도구에서 자동 인식하여 코드 자동 완성 등 지원
4. **프레임워크 호환성(Framework Compatibility)** - Spring, Hibernate 등의 프레임워크와의 원활한 통합

## 실제 활용 예시

### Spring 프레임워크에서의 활용

```java
@Entity
public class Product implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private double price;
    
    // 기본 생성자
    public Product() {
    }
    
    // getter/setter 메서드들
    public Long getId() {
        return id;
    }
    
    public void setId(Long id) {
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public double getPrice() {
        return price;
    }
    
    public void setPrice(double price) {
        this.price = price;
    }
}
```

Spring과 같은 프레임워크는 위의 자바빈 규약을 따르는 클래스를 활용하여 의존성 주입, ORM 매핑 등을 수행합니다.

## 결론

자바빈 프로퍼티 규약은 자바 생태계에서 객체 데이터를 표현하고 조작하는 표준 방식입니다. 이 규약을 따름으로써 다양한 프레임워크와의 호환성을 확보하고, 객체의 상태를 안전하게 관리할 수 있습니다. 