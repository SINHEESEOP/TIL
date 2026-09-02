# 객체 지향 설계의 SOLID 원칙 가이드

## 1. 단일 책임 원칙 (Single Responsibility Principle)

### 1.1 핵심 개념
- **한 클래스는 단 하나의 책임만 가져야 한다**
- 변경의 이유가 하나여야 한다

### 1.2 실제 예시
```java
// 잘못된 예시: 여러 책임이 혼재
public class UserService {
    public void createUser(User user) { /* 사용자 생성 로직 */ }
    public void sendEmail(User user) { /* 이메일 발송 로직 */ }
    public void generateReport(User user) { /* 리포트 생성 로직 */ }
}

// 좋은 예시: 책임 분리
public class UserService {
    public void createUser(User user) { /* 사용자 생성 로직 */ }
}

public class EmailService {
    public void sendEmail(User user) { /* 이메일 발송 로직 */ }
}

public class ReportService {
    public void generateReport(User user) { /* 리포트 생성 로직 */ }
}
```

## 2. 개방-폐쇄 원칙 (Open-Closed Principle)

### 2.1 핵심 개념
- **확장에는 열려있고, 수정에는 닫혀있어야 한다**
- 기존 코드 변경 없이 새로운 기능을 추가할 수 있어야 함

### 2.2 실제 예시
```java
// 잘못된 예시: 새로운 할인 정책 추가시 코드 수정 필요
public class DiscountCalculator {
    public double calculateDiscount(Order order, String type) {
        if (type.equals("REGULAR")) {
            return order.getPrice() * 0.1;
        } else if (type.equals("VIP")) {
            return order.getPrice() * 0.2;
        }
        return 0;
    }
}

// 좋은 예시: 인터페이스를 통한 확장
public interface DiscountPolicy {
    double calculateDiscount(Order order);
}

public class RegularDiscountPolicy implements DiscountPolicy {
    @Override
    public double calculateDiscount(Order order) {
        return order.getPrice() * 0.1;
    }
}

public class VIPDiscountPolicy implements DiscountPolicy {
    @Override
    public double calculateDiscount(Order order) {
        return order.getPrice() * 0.2;
    }
}
```

## 3. 리스코프 치환 원칙 (Liskov Substitution Principle)

### 3.1 핵심 개념
- **상위 타입의 객체를 하위 타입의 객체로 치환해도 프로그램의 정확성이 보장되어야 한다**
- 하위 클래스는 상위 클래스의 규약을 지켜야 함

### 3.2 실제 예시
```java
// 잘못된 예시: LSP 위반
public class Rectangle {
    protected int width;
    protected int height;
    
    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    public int getArea() { return width * height; }
}

public class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width;  // LSP 위반: 예상치 못한 동작
    }
}

// 좋은 예시: 공통 인터페이스 사용
public interface Shape {
    int getArea();
}

public class Rectangle implements Shape {
    private int width;
    private int height;
    
    public int getArea() { return width * height; }
}

public class Square implements Shape {
    private int side;
    
    public int getArea() { return side * side; }
}
```

## 4. 인터페이스 분리 원칙 (Interface Segregation Principle)

### 4.1 핵심 개념
- **클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다**
- 큰 인터페이스를 작은 단위로 분리

### 4.2 실제 예시
```java
// 잘못된 예시: 거대한 인터페이스
public interface Worker {
    void work();
    void eat();
    void sleep();
}

// 좋은 예시: 인터페이스 분리
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

public interface Sleepable {
    void sleep();
}

public class Human implements Workable, Eatable, Sleepable {
    @Override public void work() { /* 작업 수행 */ }
    @Override public void eat() { /* 식사 */ }
    @Override public void sleep() { /* 수면 */ }
}

public class Robot implements Workable {
    @Override public void work() { /* 작업 수행 */ }
}
```

## 5. 의존관계 역전 원칙 (Dependency Inversion Principle)

### 5.1 핵심 개념
- **고수준 모듈은 저수준 모듈에 의존하지 않아야 한다**
- 둘 다 추상화에 의존해야 함

### 5.2 실제 예시
```java
// 잘못된 예시: 구체 클래스에 직접 의존
public class OrderService {
    private MySQLOrderRepository orderRepository = new MySQLOrderRepository();
    
    public void createOrder(Order order) {
        orderRepository.save(order);
    }
}

// 좋은 예시: 추상화에 의존
public interface OrderRepository {
    void save(Order order);
}

public class MySQLOrderRepository implements OrderRepository {
    @Override
    public void save(Order order) { /* MySQL 저장 로직 */ }
}

public class OrderService {
    private final OrderRepository orderRepository;
    
    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }
    
    public void createOrder(Order order) {
        orderRepository.save(order);
    }
}
```

## SOLID 원칙의 실제 적용

### 장점
1. 유지보수성 향상
2. 재사용성 증가
3. 테스트 용이성
4. 확장성 개선

### 주의사항
- 과도한 추상화 피하기
- 비즈니스 요구사항에 맞는 적절한 수준의 설계
- 점진적인 리팩토링을 통한 개선

## 결론
SOLID 원칙은 객체 지향 설계의 근간이 되는 원칙들입니다. 이러한 원칙들을 잘 이해하고 적용하면, 유지보수가 쉽고 확장 가능한 소프트웨어를 만들 수 있습니다. 하지만 각 원칙을 맹목적으로 따르기보다는, 상황에 맞게 적절히 적용하는 것이 중요합니다. 