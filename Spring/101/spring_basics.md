
## 목차

1. 정적 컨텐츠 
2. MVC와 템플릿 엔진
3. API


## 정적 컨텐츠

### 정적 컨텐츠란?
- **스프링 부트 정적 컨텐츠 기능**: `resources/static` 폴더 안의 파일을 웹 브라우저 요청 그대로 반환 (HTML, CSS, JS 등).  
- 예시: `resources/static/hello-static.html`

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>static content</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
정적 컨텐츠 입니다.
</body>
</html>
```

- **실행 URL**: `http://localhost:8080/hello-static.html`
  - 브라우저에서 접근 시 해당 HTML이 그대로 응답됨.

> **핵심 요약**  
> - 정적 컨텐츠: 서버에서 파일을 **그대로** 내려줌 (별도의 서버 로직 없음).  
> - 스프링 부트는 `resources/static` 경로 아래의 파일들을 기본적으로 정적 리소스로 제공함.


## MVC와 템플릿 엔진

### MVC(Model-View-Controller)
- **Model**: 데이터 및 비즈니스 로직을 담당  
- **View**: 화면을 담당  
- **Controller**: 웹 요청을 받아 비즈니스 로직 및 모델을 호출하고, 뷰에 결과 전달

### Controller 예시

```java
@Controller
public class HelloController {

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template"; // 뷰 이름
    }
}
```
- `@GetMapping("hello-mvc")`: `GET /hello-mvc` 요청을 매핑  
- `@RequestParam("name")`: 쿼리 파라미터로 넘어오는 `name` 값을 받음  
- `model.addAttribute("name", name)`: 뷰로 전달할 데이터 설정  
- 리턴값 `"hello-template"`는 **템플릿 파일** 이름.

### View 예시 (Thymeleaf 템플릿)

`resources/templates/hello-template.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```
- `${name}`: 모델로부터 전달받은 `name` 속성을 사용  
- **실행 URL**: `http://localhost:8080/hello-mvc?name=spring`

> **핵심 요약**  
> - **MVC 패턴**을 통해 **역할을 분리**하고, 템플릿 엔진(Thymeleaf 등)을 사용하면 동적 HTML을 생성 가능.  
> - **Controller**에서 뷰 템플릿으로 데이터를 전달하고, 템플릿은 해당 데이터를 렌더링하여 HTML 응답을 반환.


## API

### `@ResponseBody` 문자 반환

```java
@Controller
public class HelloController {

    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name;
    }
}
```
- `@ResponseBody`: 뷰 리졸버 대신 **HTTP 응답 바디**에 **문자**를 직접 전송  
- **실행 URL**: `http://localhost:8080/hello-string?name=spring`  
- 결과: `"hello spring"` (문자열 그대로 반환)

### `@ResponseBody` 객체 반환(JSON)

```java
@Controller
public class HelloController {

    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello; // 객체 반환
    }

    static class Hello {
        private String name;
        // getter, setter
    }
}
```
- **실행 URL**: `http://localhost:8080/hello-api?name=spring`  
- **결과**: JSON 형태로 응답. 예: `{"name":"spring"}`

### `@ResponseBody` 사용 원리
- 뷰 리졸버 대신 **HttpMessageConverter**가 동작:  
  - 문자열 반환 → `StringHttpMessageConverter`  
  - 객체 반환 → `MappingJackson2HttpMessageConverter`  
- **클라이언트의 `Accept` 헤더**와 **서버 컨트롤러의 반환 타입** 등을 참고하여 적절한 `HttpMessageConverter`가 선택됨.

> **핵심 요약**  
> - `@ResponseBody`로 **문자열**, **JSON** 등을 직접 반환.  
> - **HttpMessageConverter**가 이를 처리하여 최종적으로 HTTP 바디에 JSON(혹은 문자열 등)을 담아 전송.  
> - 스프링 MVC는 **컨텐츠 협상(Content Negotiation)**을 통해 **클라이언트-서버 간에 데이터 형식을 결정**함.


## 결론: 스프링 웹 개발 기초
- **정적 컨텐츠**: 변하지 않는 파일(HTML 등)을 직접 제공  
- **MVC + 템플릿 엔진**: 서버에서 동적 HTML 뷰 생성 → 클라이언트에 전송  
- **API**: `@ResponseBody` 사용 시 JSON 등으로 **데이터**를 직접 반환  

이로써 간단한 웹 애플리케이션을 만들 때 **정적 파일**, **동적 HTML**, **JSON API** 방식을 모두 다룰 수 있으며, 상황에 맞춰 적절히 선택 가능하다.

---

### 참고 자료
- [Spring Boot Docs - Static Content](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)
- [스프링 공식 레퍼런스](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html)
- [MVC와 템플릿 엔진 예시](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-template-engines)


> **Markdown 작성 가이드**  
> - 상기 예시는 깃북 업로드를 위한 완성형 Markdown 예시입니다.  
> - 실제 깃북에서 **문서 트리**(Summary)를 구성할 때, 필요하다면 `README.md` 또는 여러 챕터 파일(`chapter1.md`, `chapter2.md` 등)로 분할할 수 있습니다.  
> - 문서 최상단에 목차를 생성하여, 각 섹션을 내부 링크로 연결 (깃북에서 자동 TOC 기능 활용 가능).  