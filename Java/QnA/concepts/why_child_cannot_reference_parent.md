# 자식 타입에 부모 타입을 담을 수 없는 근본적인 이유

### 1. **타입 안정성(type safety)**

- **타입 안정성(type safety)**이란, **잘못된 타입을 할당하거나 호출하는 것을 방지하여 런타임 오류를 예방하는 개념**입니다.
- 만약 **자식 타입의 변수에 부모 타입의 객체를 넣을 수 있다면, 런타임 시 잘못된 메서드를 호출할 위험**이 생깁니다.
- 따라서, **컴파일 오류가 발생하는 이유는 타입 안정성을 보장하기 위한 설계적 결정**이라고 볼 수 있습니다.

즉, 사용자의 의견이 맞으며, 이를 더 깊게 분석하여 **왜 타입 안정성이 중요한지**, 그리고 **어떤 문제가 발생하는지** 설명하겠습니다.


### **2. 자식 타입 변수에 부모 타입 객체를 담으면 안 되는 이유 (근본적인 원인 분석)**

### **(1) 기본 원칙: "is-a 관계"를 따른다**

- 객체지향에서 **다형성(polymorphism)** 개념에 따라 **부모 타입 변수에는 자식 객체를 담을 수 있지만, 그 반대는 불가능**합니다.
- 즉,
    
    ```java
    Parent p = new Child();  // ✅ 가능 (업캐스팅, Upcasting)
    Child c = new Parent();  // ❌ 불가능 (다운캐스팅 오류)
    
    ```
    
    - `Child`가 `Parent`를 포함하는 개념이므로, `Parent`는 `Child`가 될 수 없습니다.

### **(2) 컴파일러가 막는 이유 → "호환되지 않는 메서드 호출 가능성"**

부모 객체는 **자식 클래스에 없는 기능(메서드, 필드 등)을 가지고 있을 가능성이 큽니다**.

즉, 자식 타입 변수에 부모 객체를 담으면 **정의되지 않은 메서드를 호출할 가능성이 있기 때문에** 컴파일러가 이를 막는 것입니다.

### **예제 1: 컴파일 오류 예시**

```java
class Parent {
    void parentMethod() {
        System.out.println("Parent method");
    }
}

class Child extends Parent {
    void childMethod() {
        System.out.println("Child method");
    }
}

public class Main {
    public static void main(String[] args) {
        Child c = new Parent();  // ❌ 컴파일 오류
        c.childMethod();  // 자식 타입에 부모 객체를 담았으므로, childMethod() 호출 불가능
    }
}
```

- 위 코드가 허용된다면, `c.childMethod()`를 호출하려 할 때 **런타임 오류가 발생할 가능성**이 있음.
- 하지만 Java는 **컴파일 타임에서 이를 미리 차단**하여 오류를 예방함.


### **3. 만약 Java에서 허용한다면 어떤 문제가 발생할까? (위험성 분석)**

자바가 **"자식 타입 변수에 부모 타입 객체를 담는 것"을 허용했다고 가정**하고, 실제 실행했을 때 발생할 수 있는 문제를 살펴보겠습니다.

### **예제 2: 잘못된 메서드 호출로 인한 런타임 오류 발생 가능성**

```java
class Parent {
    void parentMethod() {
        System.out.println("Parent method");
    }
}

class Child extends Parent {
    void childMethod() {
        System.out.println("Child method");
    }
}

public class Main {
    public static void main(String[] args) {
        Child c = (Child) new Parent(); // ⚠️ 강제 캐스팅 (실제로는 부모 객체)
        c.childMethod(); // ❌ 런타임 오류 발생
    }
}

``` 

위 코드에서 **Parent 객체를 Child 타입으로 강제 캐스팅**하면, `childMethod()`가 실제로 존재하지 않기 때문에 **런타임 오류(ClassCastException)**가 발생합니다.

**Java에서 자식 타입 변수에 부모 타입을 담을 수 없도록 막는 이유는 바로 이 문제를 예방하기 위해서입니다.**

즉, **타입 안정성을 보장하고, 런타임 오류를 미리 방지**하는 것입니다.


### **4. 결론: 타입 안정성과 컴파일 타임 안전성 보장**

- **Java에서 "자식 타입 변수에 부모 객체를 담을 수 없는 이유"는 타입 안정성을 보장하기 위해서이다.**
- 이를 허용하면, **정의되지 않은 메서드를 호출할 가능성이 생기며, 런타임 오류(ClassCastException)가 발생할 위험**이 있음.
- 따라서 Java는 **컴파일 단계에서 이를 차단하여 오류를 미리 방지**하고 있음.

**즉, 컴파일 오류가 발생하는 것이 문제가 아니라, 이를 허용하면 더 큰 문제(런타임 오류)가 발생하기 때문**에 Java에서 이를 엄격히 금지하는 것입니다.

사용자의 의견처럼 **타입 안정성이 근본적인 원인**이며, 이는 **코드의 안전성을 보장하기 위한 언어 설계적 결정**입니다. 🚀