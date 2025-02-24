## 생성자
생성자란 객체를 생성할 때 초기화를 담당하는 메서드이다. 생성자는 클래스 이름과 같아야 하며, 반환 타입이 없다.
- 추가로 생성자를 선언하지 않아도 기본 생성자는 자동으로 생성된다. 단, 생성자가 하나라도 있으면 기본 생성자는 자동으로 생성되지 않는다.

### 생성자 필요성
- 객체 생성 시 초기화 작업이 필요한 경우
- 객체 생성 시 매개변수를 받아서 초기화 작업이 필요한 경우
- 객체 생성 시 여러 객체가 공통적으로 사용하는 초기화 작업이 필요한 경우

### 생성자의 필요성2 (제약을 걸 수 있다.)
- 생성자를 사용하면 필수값 입력을 보장할 수 있다.
    - 생성자를 직접 정의하면 기본 생성자는 자동으로 생성되지 않는다는 특성을 이용하는 방법이다.
>좋은 프로그램은 무한한 자유도가 주어지는 것이 아니라, 제약을 걸어서 오류를 방지하는 것이 중요하다. 

<br>

## **생성자 오버로딩과 this()**

### **생성자 오버로딩**

- 매개변수 구성이 다른 여러 개의 생성자를 제공 가능.
- 단, 아래의 코드의 경우는 굳이 this를 사용하지 않아도 된다.
> intellij IDE 에서는 this 를 사용하지 않아도 색을 통해 쉽게 구분할 수 있어, 꼭 필요한 경우 아니면 굳이 사용할 필욘 없다고 생각한다.

```java
public class MemberConstruct {
    String name;
    int age;
    int grade;

    MemberConstruct(String name, int age) {
        this.name = name;
        this.age = age;
        this.grade = 50;
    }

    MemberConstruct(String name, int age, int grade) {
        this.name = name;
        this.age = age;
        this.grade = grade;
    }
}
```

### **this()를 사용한 중복 제거**

- **this()를 사용하면 하나의 생성자에서 다른 생성자를 호출 가능**.

```java
public class MemberConstruct {
    String name;
    int age;
    int grade;

    MemberConstruct(String name, int age) {
        this(name, age, 50);  // this() 사용하여 다른 생성자 호출
    }

    MemberConstruct(String name, int age, int grade) {
        this.name = name;
        this.age = age;
        this.grade = grade;
    }
}
```

### **this() 규칙**

- `this()`는 **반드시 생성자 코드의 첫 줄에 위치해야 함**.

```java
public MemberConstruct(String name, int age) {
    System.out.println("생성자 시작");  // ⚠️ this() 앞에 코드가 있으면 오류 발생
    this(name, age, 50);
```
### this 사용 TIP
- 인틀리제이의 경우 this 를 사용하지 않아도 색을 통해 쉽게 구분할 수 있어, 꼭 필요한 경우 아니면 굳이 사용할 필욘 없다고 생각한다.


