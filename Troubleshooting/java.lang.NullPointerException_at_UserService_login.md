# java.lang.NullPointerException at UserService.login (해당 파일은 예시 템플릿 파일입니다.)

## 1. 에러 로그 및 현상

로그인 API 호출 시 500 에러와 함께 서버 콘솔에 아래 에러 로그가 출력됨.

```text
java.lang.NullPointerException: Cannot invoke "com.example.User.getPassword()" because "user" is null
    at com.example.UserService.login(UserService.java:18)
    at com.example.UserController.login(UserController.java:24)
```

## 2. 발생 원인

DB에서 입력받은 이메일로 회원을 조회했으나 존재하지 않아 `null`이 반환되었음.
조회된 회원 객체가 `null`인지 검증하지 않고 바로 `user.getPassword()`를 호출하여 NullPointerException이 발생함.

```java
// UserService.java Line 18
User user = userRepository.findByEmail(email);
if (user.getPassword().equals(encryptPassword)) { // user가 null일 때 NPE 발생
    return true;
}
```

## 3. 해결 방법

조회 결과가 `null`인 경우에 대해 예외 처리를 추가하고, 안전한 비교(Object equals)를 하도록 변경함.

### 수정 후

```java
User user = userRepository.findByEmail(email);
if (user == null) {
    throw new IllegalArgumentException("존재하지 않는 회원입니다.");
}

if (!user.getPassword().equals(encryptPassword)) {
    throw new IllegalArgumentException("비밀번호가 일치하지 않습니다.");
}
```

## 4. 요약 및 예방

DB 조회 결과처럼 `null`을 반환할 수 있는 객체는 참조하기 전에 반드시 `null` 체크를 수행해야 함.
향후 Java 8의 `Optional`을 사용하도록 Repository를 리팩토링하여 `null` 가능성을 컴파일 단계에서 명시적으로 처리할 예정임.
