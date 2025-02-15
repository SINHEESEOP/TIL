# ☕ Java Basics

## 📝 Java란?

Java는 썬 마이크로시스템즈에서 개발한 객체지향 프로그래밍 언어입니다. "Write Once, Run Anywhere"라는 슬로건처럼 플랫폼 독립적인 특징을 가지고 있습니다.

## 🌟 Java의 특징

1. **객체지향 (Object-Oriented)**
   - 모든 것이 객체로 구성
   - 캡슐화, 상속, 다형성 지원

2. **플랫폼 독립성**
   - JVM을 통해 어느 플랫폼에서나 실행 가능
   - 바이트코드로 컴파일되어 실행

3. **강력한 타입 체크**
   - 정적 타입 언어
   - 컴파일 시점에서 타입 오류 검출

4. **자동 메모리 관리**
   - 가비지 컬렉션을 통한 자동 메모리 관리
   - 메모리 누수 방지

## 💻 기본 문법

### 1. 기본 구조
```java
// 패키지 선언
package com.example;

// 클래스 선언
public class HelloWorld {
    // main 메서드 - 프로그램의 시작점
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

### 2. 변수 선언
```java
// 기본 데이터 타입
int number = 10;
double decimal = 3.14;
boolean isTrue = true;
char character = 'A';

// 참조 타입
String text = "Hello";
```

### 3. 제어문
```java
// if-else 문
if (condition) {
    // 조건이 참일 때 실행
} else {
    // 조건이 거짓일 때 실행
}

// for 반복문
for (int i = 0; i < 5; i++) {
    // 반복 실행할 코드
}

// while 반복문
while (condition) {
    // 조건이 참인 동안 반복
}
```

## 🎯 Java 프로그램 실행 과정

1. **소스 코드 작성** (.java 파일)
2. **컴파일** (javac 명령어로 .class 파일 생성)
3. **JVM에서 실행** (java 명령어로 실행)

```bash
# 컴파일
javac HelloWorld.java

# 실행
java HelloWorld
```

## 📦 Java 개발 환경 설정

1. **JDK 설치**
   - [Oracle JDK](https://www.oracle.com/java/technologies/downloads/)
   - [OpenJDK](https://adoptopenjdk.net/)

2. **환경 변수 설정**
   - JAVA_HOME 설정
   - Path에 Java 실행 파일 경로 추가

3. **IDE 설치**
   - [IntelliJ IDEA](https://www.jetbrains.com/idea/)
   - [Eclipse](https://www.eclipse.org/)
   - [VS Code](https://code.visualstudio.com/)

## 🔍 다음 학습 내용
- [객체지향 프로그래밍](oop.md)
- [데이터 타입](data-types.md)
- [제어문](control-flow.md)
- [배열](array.md) 