## 1. 상속과 포함의 차이

상속(Inheritance)과 포함(Composition)은 객체 지향 프로그래밍에서 서로 다른 개념이다.

- **포함(Composition)**: 클래스의 멤버 변수로 다른 객체를 포함하는 방식
  - 특정 객체가 다른 객체의 참조를 멤버 변수로 가지는 형태
  - 예시: `class Car { Engine engine; }`
  
- **상속(Inheritance)**: 자식 클래스가 부모 클래스를 확장하는 방식
  - 자식 클래스 내부에 부모 클래스의 모든 필드와 메서드가 포함됨
  - 예시: `class Child extends Parent {}`

## 2. super() 생성자

`super()`는 부모 클래스의 생성자를 호출하는 키워드이다. `this`와 유사한 개념으로, `super.메서드()`를 통해 부모 클래스의 메서드나 변수를 참조할 수 있다.

### super()의 기본 규칙
1. **자식 클래스의 생성자에서 부모 생성자를 명시적으로 호출할 수 있다.**
2. **기본적으로 부모의 기본 생성자(`super()`)가 자동 호출된다.**
3. **부모 클래스에 기본 생성자가 없으면 명시적으로 호출해야 한다.**

### 예제 코드
```java
class Parent {
    Parent() {
        System.out.println("Parent 생성자 호출");
    }
}

class Child extends Parent {
    Child() {
        super(); // 부모 생성자 호출 (생략 가능)
        System.out.println("Child 생성자 호출");
    }
}

public class Main {
    public static void main(String[] args) {
        Child child = new Child();
    }
}
```
**출력 결과:**
```
Parent 생성자 호출
Child 생성자 호출
```

## 3. 상속과 메모리 구조

상속을 활용할 때, 객체의 메모리 구조는 다음과 같이 구성된다.

### 예제 코드
```java
class Parent {
    int parentVar = 10;
    void parentMethod() { System.out.println("Parent method"); }
}

class Child extends Parent {
    int childVar = 20;
    void childMethod() { System.out.println("Child method"); }
}

public class Main {
    public static void main(String[] args) {
        Parent parent = new Child(); // 부모 타입으로 자식 객체 참조
        Child child = new Child(); // 자식 타입으로 자식 객체 참조
    }
}
```

### 1) 부모 타입으로 자식 객체 참조
```java
Parent parent = new Child();
```
- **자식 객체를 생성하지만, 부모 타입으로 참조**
- **부모 클래스의 변수 및 메서드만 접근 가능** (`parentMethod()` 호출 가능, `childMethod()` 호출 불가능)
- **실제 객체는 자식이지만, 부모 타입으로 선언했기 때문에 부모의 기능만 사용 가능**

### 2) 자식 타입으로 자식 객체 참조
```java
Child child = new Child();
```
- **자식 객체를 생성하고, 자식 타입으로 참조**
- **부모와 자식 클래스의 모든 기능을 사용 가능**
- **단, 부모의 `private` 필드는 접근 불가능**

## 4. 결론

1. **상속은 자식 클래스가 부모 클래스를 확장하는 개념이며, 자식 객체 안에 부모 객체가 포함됨**
2. **포함은 객체의 멤버 변수로 다른 객체를 포함하는 방식**
3. **super()는 부모 생성자를 호출하며, 부모 클래스의 기능을 활용할 때 사용**
4. **부모 타입으로 자식 객체를 참조하면 부모의 기능만 사용 가능**
5. **자식 타입으로 자식 객체를 참조하면 부모와 자식의 모든 기능을 사용 가능 (단, private 멤버 제외)**