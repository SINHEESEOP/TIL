# Final 키워드 정리

`final` 키워드는 **값을 한 번 할당하면 변경할 수 없도록 고정하는 역할**을 한다.

## 1. Final 변수 사용 예시

### 1.1 필드 변수의 값 변경 방지
- 객체가 생성될 때 한 번만 값을 할당하고 이후 변경되지 않도록 할 때 사용한다.
- 예: 사용자 ID는 한 번 설정되면 변경할 수 없도록 `final`을 사용할 수 있다.

```java
class User {
    private final String userId;
    
    public User(String userId) {
        this.userId = userId;
    }
    
    public String getUserId() {
        return userId;
    }
}
```

### 1.2 메서드 매개변수 값 변경 방지
- 메서드 내부에서 매개변수 값을 변경할 수 없도록 한다.
- 코드의 안정성을 높이고, 예기치 않은 값 변경을 방지할 수 있다.

```java
void printUserInfo(final String name) {
    // name = "Other Name"; // 컴파일 오류 발생 (final 변수는 변경 불가)
    System.out.println("User: " + name);
}
```

### 1.3 특정 값 고정하여 일관성 유지
- 메서드 내에서 변경되지 않아야 하는 특정 값을 `final`로 선언하여 코드의 일관성을 유지할 수 있다.

```java
void calculate(final int base) {
    int result = base * 10; // base 값이 변하지 않음을 보장
    System.out.println(result);
}
```


## 2. static final (상수)란?
`static final` 키워드는 **프로그램 실행 중 변경되지 않는 값을 정의하는 상수**를 만들 때 사용한다.

### 2.1 왜 static을 추가하는가?
- `final`만 사용하면 각 객체마다 해당 변수가 개별적으로 존재하게 된다.
- `static final`을 사용하면 **클래스 레벨에서 공유되는 단 하나의 값**을 유지할 수 있다.
- 이를 통해 **메모리 사용을 최적화**하고 **코드 중복을 방지**할 수 있다.

```java
class Taxi {
    public static final int MAX_PASSENGERS = 4;
}

System.out.println(Taxi.MAX_PASSENGERS); // 4 (객체를 생성하지 않아도 접근 가능)
```

### 2.2 상수의 네이밍 규칙
- **자바 컨벤션에 따라 상수명은 대문자 + 스네이크 케이스(SNAKE_CASE) 사용**
- 예: `MAX_SPEED`, `DEFAULT_TIMEOUT`

```java
public static final int MAX_SPEED = 120;
public static final String DEFAULT_LANGUAGE = "EN";
```

---

## 3. Final 키워드를 클래스와 메서드에 적용할 경우

### 3.1 클래스에 `final` 적용
- `final` 키워드가 붙은 클래스는 **상속이 불가능**하다.
- 예: `String` 클래스는 `final`로 선언되어 있어 확장할 수 없다.

```java
final class Constants {
    public static final double PI = 3.14159;
}

// class MyConstants extends Constants {} // 컴파일 오류 발생 (final 클래스는 상속 불가)
```

### 3.2 메서드에 `final` 적용
- `final` 메서드는 **오버라이딩(Override)을 방지**한다.
- 하지만 **오버로딩(Overloading)은 가능**하다.

```java
class Parent {
    public final void show() {
        System.out.println("Cannot be overridden");
    }
}

class Child extends Parent {
    // public void show() {} // 컴파일 오류 발생 (final 메서드는 오버라이딩 불가)
}
```

---

## 4. 결론
- `final` 키워드는 **변수, 메서드, 클래스에 적용할 수 있으며, 변경을 방지하는 역할**을 한다.
- `static final`을 사용하면 **프로그램 전체에서 공유되는 상수**를 만들 수 있다.
- 클래스에 적용하면 **상속을 막고**, 메서드에 적용하면 **오버라이딩을 방지**할 수 있다.

> **💡 요약:** `final`은 값 변경 방지, `static final`은 상수를 만들기 위해 사용하며, 클래스/메서드에 적용하면 각각 상속과 오버라이딩을 제한한다.

