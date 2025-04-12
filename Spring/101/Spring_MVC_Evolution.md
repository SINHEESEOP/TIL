# 🚀 서블릿에서 스프링 MVC로의 발전 과정과 핵심 구조

## 1️⃣ 전통적인 서블릿 개발 방식의 한계점

### 초기 서블릿 개발 방식
- **서블릿 클래스 직접 구현** 📝
  - `HttpServlet`을 상속하고 `doGet()`, `doPost()` 메서드 오버라이딩
  - URL과 서블릿 매핑을 위해 `web.xml`에 직접 등록하는 방식
- **모든 기능이 혼재** 🔄
  - 하나의 서블릿 클래스에서 HTTP 요청 처리, 비즈니스 로직, 데이터 접근, 응답 생성을 모두 담당
  - 코드 재사용성이 낮고 유지보수가 어려움
  
```java
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 파라미터 추출
        String name = req.getParameter("name");
        
        // 비즈니스 로직 수행
        String result = "Hello, " + name;
        
        // 응답 생성
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();
        out.println("<html><body>");
        out.println("<h1>" + result + "</h1>");
        out.println("</body></html>");
    }
}
```

### MVC 패턴의 등장
- **역할 분리의 필요성** 🧩
  - 표현 로직(View)과 비즈니스 로직(Model)을 분리해야 한다는 인식 확산
  - 복잡도 증가에 따른 구조적 개선 필요성 대두
- **서블릿 기반 MVC 패턴 적용** 🏗️
  - 컨트롤러 역할의 서블릿이 요청을 받고 적절한 모델을 호출
  - JSP를 이용한 뷰 처리 분리
  - RequestDispatcher를 통한 포워딩 방식 적용

```java
// 컨트롤러 역할의 서블릿
public class MemberController extends HttpServlet {
    private MemberService memberService = new MemberService();
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 요청 처리 및 모델 호출
        List<Member> members = memberService.findAllMembers();
        
        // 결과를 request에 저장
        req.setAttribute("members", members);
        
        // JSP로 포워딩
        RequestDispatcher dispatcher = req.getRequestDispatcher("/WEB-INF/views/members.jsp");
        dispatcher.forward(req, resp);
    }
}
```

## 2️⃣ 모델2 아키텍처와 프론트 컨트롤러 패턴

### 모델2 아키텍처
- **JSP Model 2 아키텍처** 📊
  - 서블릿이 컨트롤러 역할, JSP가 뷰 역할을 담당하는 구조
  - 모델은 JavaBean이나 별도 서비스 클래스가 담당
- **한계점** ⚠️
  - 컨트롤러 서블릿이 많아지면서 중복 코드 발생
  - URL 매핑과 처리 로직이 분산되어 관리 어려움

### 프론트 컨트롤러 패턴의 도입
- **단일 진입점 개념 도입** 🚪
  - 모든 요청을 하나의 컨트롤러가 먼저 받는 구조
  - 공통 로직을 중앙화하고 요청을 적절한 핸들러에 위임
- **프론트 컨트롤러 설계** 🎮
  - 요청 분석 및 매핑
  - 공통 전처리 로직(인증, 권한 체크 등)
  - 핸들러 호출 및 뷰 반환 처리

```java
// 프론트 컨트롤러 패턴 적용 예시
public class FrontControllerServlet extends HttpServlet {
    private Map<String, Controller> controllerMap = new HashMap<>();
    
    @Override
    public void init() throws ServletException {
        // URL과 컨트롤러 매핑 등록
        controllerMap.put("/members", new MemberListController());
        controllerMap.put("/member/new", new MemberFormController());
        // ...
    }
    
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String requestURI = req.getRequestURI();
        Controller controller = controllerMap.get(requestURI);
        
        if (controller == null) {
            resp.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        
        // 컨트롤러 실행 및 뷰 경로 반환
        String viewPath = controller.process(req, resp);
        
        // 뷰로 포워딩
        RequestDispatcher dispatcher = req.getRequestDispatcher(viewPath);
        dispatcher.forward(req, resp);
    }
}

// 컨트롤러 인터페이스
interface Controller {
    String process(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException;
}
```

## 3️⃣ 스프링 MVC의 등장과 핵심 구조

### 스프링 MVC 프레임워크의 등장
- **DispatcherServlet의 도입** 🌟
  - 스프링이 제공하는 프론트 컨트롤러 서블릿
  - `HttpServlet`을 상속받아 서블릿 규약을 준수하면서 기능 확장
- **어노테이션 기반 설정** 📌
  - XML 설정에서 점차 어노테이션 기반으로 전환
  - `@Controller`, `@RequestMapping` 등을 통한 선언적 프로그래밍

### 스프링 MVC의 핵심 구성요소
- **DispatcherServlet** 🎯
  - 모든 웹 요청의 진입점(프론트 컨트롤러)
  - 요청을 적절한 핸들러에 위임하고 응답 생성 과정 조율
- **HandlerMapping** 🗺️
  - 요청 URL을 어떤 핸들러가 처리할지 결정
  - `RequestMappingHandlerMapping` 등 여러 구현체 존재
- **HandlerAdapter** 🔌
  - 핸들러를 실행하기 위한 어댑터
  - 다양한 형태의 핸들러(컨트롤러)를 지원
- **ViewResolver** 🖼️
  - 핸들러가 반환한 뷰 이름을 실제 뷰 객체로 변환
  - JSP, Thymeleaf 등 다양한 템플릿 엔진 지원
- **Model** 💼
  - 컨트롤러에서 뷰로 전달할 데이터 포함
  - `Model`, `ModelAndView` 등으로 표현

```java
@Controller
public class MemberController {
    private final MemberService memberService;
    
    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
    
    @GetMapping("/members")
    public String members(Model model) {
        model.addAttribute("members", memberService.findMembers());
        return "members/memberList";
    }
}
```

### 스프링 부트와 MVC 자동 구성
- **내장 서블릿 컨테이너** 📦
  - 별도의 톰캣 설치 없이 애플리케이션 실행 가능
  - `spring-boot-starter-web`에 포함된 내장 톰캣 사용
- **자동 설정** ⚙️
  - DispatcherServlet 자동 등록 및 설정
  - 뷰 리졸버, 정적 리소스 등 기본 설정 제공
- **스타터와 의존성 관리** 📚
  - 필요한 라이브러리를 편리하게 가져오는 스타터 패키지
  - 호환되는 버전 자동 관리

## 4️⃣ 스프링 MVC의 요청 처리 흐름

### 스프링 MVC 요청 처리 과정
1. **클라이언트 요청 접수** 🌐
   - 클라이언트의 HTTP 요청이 서블릿 컨테이너에 도착
   - 서블릿 컨테이너는 요청을 DispatcherServlet에 전달
   
2. **핸들러 매핑 및 실행** 🔍
   - DispatcherServlet은 HandlerMapping을 통해 요청을 처리할 핸들러 탐색
   - 적절한 HandlerAdapter를 선택하여 핸들러(컨트롤러) 실행
   
3. **모델 생성 및 뷰 선택** 📊
   - 핸들러는 비즈니스 로직을 처리하고 Model 객체에 데이터 추가
   - 논리적 뷰 이름 반환
   
4. **뷰 렌더링** 🎨
   - ViewResolver가 논리적 뷰 이름을 실제 View 객체로 변환
   - View 객체가 Model 데이터를 이용해 응답 생성
   
5. **응답 반환** 📤
   - 생성된 응답이 DispatcherServlet을 통해 서블릿 컨테이너로 전달
   - 서블릿 컨테이너는 HTTP 응답을 클라이언트에게 전송

### JSON API 처리 흐름 (RESTful 웹 서비스)
- **@ResponseBody 방식** 💾
  - 뷰를 거치지 않고 직접 HTTP 응답 바디에 데이터 반환
  - `HttpMessageConverter`가 자바 객체를 JSON 등으로 변환
- **@RestController와 통합** 🔄
  - `@Controller` + `@ResponseBody`를 결합한 어노테이션
  - API 서비스 개발을 위한 컨트롤러 구현 간소화

## 5️⃣ 스프링 MVC와 서블릿의 관계

### 레이어 구조
- **서블릿 컨테이너 (톰캣)** 🏭
  - HTTP 요청 수신 및 응답 전송 담당
  - 서블릿 라이프사이클 관리
- **DispatcherServlet (프론트 컨트롤러)** 🚀
  - 서블릿 API를 기반으로 구현된 스프링 MVC의 핵심
  - 요청 처리 워크플로우 조율
- **스프링 애플리케이션 컨텍스트** 🌱
  - 스프링 빈 관리 및 의존성 주입
  - `@Controller`, `@Service` 등 다양한 빈 라이프사이클 관리

### 역할 분담
- **톰캣(서블릿 컨테이너)** 🔄
  - 저수준 네트워크 처리 및 HTTP 프로토콜 처리
  - 스레드 관리 및 서블릿 실행 환경 제공
- **스프링 MVC (DispatcherServlet)** 🛠️
  - 요청-응답 사이클 관리 및 공통 처리
  - 컨트롤러 매핑 및 실행
- **개발자 코드 (@Controller 등)** 👨‍💻
  - 비즈니스 로직 구현에 집중
  - 선언적 방식으로 웹 요청 처리 정의

## 💡 스프링 MVC 핵심 구조 다이어그램

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        클라이언트 (브라우저)                               │
└───────────────────────────────────┬─────────────────────────────────────┘
                                    │ HTTP 요청
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          서블릿 컨테이너 (톰캣)                            │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                      DispatcherServlet                          │    │
│  │                     (프론트 컨트롤러)                             │    │
│  └────────────────────────────────┬────────────────────────────────┘    │
└───────────────────────────────────┼─────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       스프링 애플리케이션 컨텍스트                           │
│                                                                          │
│  ┌─────────────────────┐       ┌──────────────────────┐                  │
│  │   HandlerMapping    │◀────▶│    HandlerAdapter    │                  │
│  └──────────┬──────────┘       └──────────┬───────────┘                  │
│             │                             │                              │
│             │                             │                              │
│             │                             ▼                              │
│             │                ┌──────────────────────┐                    │
│             └──────────────▶│      Controller       │                   │
│                              └──────────┬───────────┘                    │
│                                         │                                │
│                                         │                                │
│                                         ▼                                │ 
│                             ┌──────────────────────┐                     │ 
│                             │    Service/Model     │                     │ 
│                             └──────────┬───────────┘                     │
│                                        │                                 │
│                                        │                                 │
│                                        ▼                                 │
│  ┌─────────────────────┐       ┌────────────────────┐                    │
│  │    ViewResolver     │◀────▶│         View       │                    │
│  └─────────────────────┘       └────────┬───────────┘                    │
└─────────────────────────────────────────┼────────────────────────────────┘
                                          │
                                          │ HTTP 응답
                                          ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        클라이언트 (브라우저)                               │
└─────────────────────────────────────────────────────────────────────────┘
```

### 스프링 MVC 흐름 설명

1. 클라이언트가 HTTP 요청을 서블릿 컨테이너(톰캣)에 전송합니다.
2. 서블릿 컨테이너는 모든 요청을 DispatcherServlet(프론트 컨트롤러)에 전달합니다.
3. DispatcherServlet은 HandlerMapping을 통해 요청을 처리할 적절한 컨트롤러를 찾습니다.
4. HandlerAdapter를 사용하여 컨트롤러의 메서드를 실행합니다.
5. 컨트롤러는 비즈니스 로직을 처리하고(Service 계층 호출), Model 데이터와 View 이름을 반환합니다.
6. ViewResolver는 논리적 뷰 이름을 실제 View 객체로 변환합니다.
7. View는 Model 데이터를 사용하여 화면을 렌더링합니다.
8. 최종 결과가 DispatcherServlet을 통해 서블릿 컨테이너로 전달됩니다.
9. 서블릿 컨테이너는 HTTP 응답을 클라이언트에게 반환합니다.

### @ResponseBody 방식의 처리 흐름

```
┌──────────────────┐        ┌──────────────────┐       ┌───────────────────ㅡㅡ─┐
│ DispatcherServlet│──────▶│   @Controller     │─────▶│ HttpMessageConverter  │
└─────────┬────────┘        │  @ResponseBody   │       │    (Jackson 등)        │
          │                 └──────────────────┘       └──────────┬───────────ㅡ┘
          │                                                     │
          ▼                                                     ▼
┌──────────────────┐                                  ┌────────────────────┐
│   클라이언트       │◀──────────────────────────────────│    JSON 응답      │
└──────────────────┘                                  └────────────────────┘
```

@ResponseBody 사용 시에는 ViewResolver 대신 HttpMessageConverter가 동작하여 객체를 JSON 등으로 직렬화하여 클라이언트에게 바로 반환합니다. 