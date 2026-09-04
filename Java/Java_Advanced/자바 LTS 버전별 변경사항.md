# 자바 LTS 버전별 변경사항

Java 8, 11, 17, 21, 25는 오라클이 장기간 패치를 보장하는 LTS(Long-Term Support) 버전이다. 실무에서 새 프로젝트의 타깃 버전을 정하거나 기존 프로젝트를 마이그레이션할 때 결국 마주치는 것도 이 다섯 버전이므로, 각 버전에서 무엇이 왜 바뀌었는지를 정리해둔다. LTS 사이사이에 나온 버전(9, 10, 12, 13 등)은 실무에서 그대로 쓰는 경우가 드물지만, LTS에 들어간 기능 대부분이 이 버전들에서 프리뷰(preview)로 먼저 시험된 뒤 넘어온 것이므로 뒤쪽에 간단히 정리한다.

## 릴리스 주기가 바뀌어온 과정

Java 7(2011)에서 Java 8(2014)까지는 약 3년, Java 8에서 Java 9(2017)까지는 프로젝트 직소(Jigsaw, 모듈 시스템) 작업이 늦어지며 3년 반이 걸렸다. 정해진 주기 없이 "기능이 다 준비되면 릴리스"하는 방식이었고, 그만큼 하나의 대형 기능이 지연되면 이미 완성된 다른 기능들까지 발이 묶였다.

이 문제를 풀기 위해 오라클의 수석 아키텍트 마크 레이놀드가 2017년 9월 "Moving Java Forward Faster"라는 글을 통해 시간 기반 릴리스를 제안했다. 6개월마다(3월, 9월) 새 버전을 내고, 그 시점에 완성된 기능만 포함하며 안 된 기능은 다음 버전으로 미룬다는 것이다. 이 방식은 JEP 322(Time-Based Release Versioning)로 공식화되어 Java 10(2018년 3월)부터 적용됐다. 처음에는 이 6개월 주기 중 세 번에 한 번(3년마다)을 LTS로 지정했다(Java 11 → 17이 정확히 3년 간격).

그런데 Java 17이 나온 2021년 9월, 오라클은 LTS 주기를 3년에서 2년으로 다시 줄인다고 발표했다. 세 번째 릴리스가 아니라 두 번째 릴리스마다 LTS로 지정하는 것으로 바뀐 것이다. 그 결과 Java 17 → 21 → 25가 모두 정확히 2년 간격이 됐고, 다음 LTS는 2027년 9월의 Java 29로 예정되어 있다.

이 짧은 주기가 필요했던 이유는 기능 하나가 실전에 자리잡기까지 거치는 과정 때문이다. 새 문법이나 API는 보통 프리뷰 기능으로 먼저 나온다. 프리뷰는 컴파일할 때 `--enable-preview` 플래그를 요구할 만큼 아직 확정되지 않은 상태라는 뜻이고, 실사용 피드백을 받아 다음 버전에서 다시 프리뷰로 나오거나(문법이 바뀌기도 한다), 최종 확정(finalize)된다. 예를 들어 스위치 표현식은 Java 12에서 처음 프리뷰로 나와 Java 14에서 최종 확정됐고, 레코드는 Java 14 프리뷰 → Java 16 확정, 가상 스레드는 Java 19 프리뷰 → Java 21 확정으로 이어졌다. LTS 주기가 짧아진 것도 이런 프리뷰 사이클이 쌓이는 속도에 맞춰 실무자들이 새 기능을 더 자주 받아볼 수 있게 하려는 목적이 크다.

> [!NOTE]
> 모든 프리뷰가 최종 확정으로 이어지는 것은 아니다. Java 21에서 프리뷰로 나온 문자열 템플릿(String Templates, JEP 430)은 Java 22에서 두 번째 프리뷰를 거친 뒤 완전히 철회됐다. 설계에 근본적인 문제가 있다고 판단되면 프리뷰 단계에서 없던 일이 되기도 한다.

## Java 8 (2014년 3월, LTS)

Java 8 이전까지는 컬렉션을 순회하며 뭔가를 처리하려면 `for` 루프를 직접 돌리거나, 콜백이 필요한 자리마다 익명 클래스를 만들어야 했다.

```java
List<String> names = Arrays.asList("kim", "lee", "park");
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});
```

정렬 기준 하나 넘기려고 클래스 선언, 어노테이션, 메서드 시그니처까지 다 써야 했다. 여기에 더해 멀티코어 CPU가 보편화되면서, 이렇게 순차적으로 도는 `for` 루프는 여러 코어를 활용할 방법이 마땅치 않다는 문제도 있었다. Java 8은 이 두 가지, 즉 "콜백을 간결하게 표현하는 문법"과 "선언적으로 병렬 처리를 표현하는 방법"을 동시에 풀기 위해 함수형 프로그래밍 요소를 언어에 들여왔다.

### 람다 표현식과 함수형 인터페이스 (JEP 126)

메서드 하나만 있는 인터페이스(함수형 인터페이스)를 구현할 때, 클래스 선언 없이 `(매개변수) -> 본문` 형태로 바로 값처럼 넘길 수 있게 됐다.

```java
Collections.sort(names, (a, b) -> a.compareTo(b));
```

동시에 인터페이스에 `default`, `static` 메서드를 추가할 수 있게 됐다. 이건 람다와 별개로 보이지만 사실 짝을 이루는 변화다. 함수형 인터페이스 개념을 언어에 넣으려면 기존 `Collection`, `List` 같은 인터페이스에도 `stream()`, `forEach()` 같은 새 메서드를 추가해야 하는데, 인터페이스에 메서드를 추가하면 그 인터페이스를 구현하던 기존 클래스가 전부 컴파일 에러가 난다. `default` 메서드는 본문을 인터페이스 안에 미리 채워둬서, 기존 구현체를 건드리지 않고도 인터페이스에 기능을 얹을 수 있게 해준다.

람다는 콜백이 잦은 코드를 확실히 짧게 만들지만, 남용하면 오히려 읽기 어려워진다. 람다 안에 로직이 몇 줄 넘게 길어지거나 중첩되면, 이름 없는 코드 블록이 스택 트레이스에서 `lambda$method$0` 같은 식으로 찍혀 디버깅할 때 어디서 터졌는지 파악하기 힘들어진다. 짧고 단순한 동작을 넘길 때 쓰는 게 맞다.

### 스트림 API (JEP 107)

컬렉션을 순회·필터링·변환·집계하는 과정을 명령형 루프 대신 선언적 파이프라인으로 표현하는 API다.

```java
List<String> longNames = names.stream()
    .filter(name -> name.length() > 3)
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

`stream()`을 `parallelStream()`으로 바꾸기만 하면 내부적으로 ForkJoinPool을 활용해 여러 코어에 작업을 나눠 처리한다. 다만 병렬 스트림은 데이터가 충분히 크고 각 처리 단계가 무거울 때만 이득이다. 데이터가 몇십 개뿐인데 병렬 스트림을 쓰면 스레드 분배 자체의 오버헤드가 더 커서 순차 처리보다 느려질 수 있다. 또 스트림 파이프라인은 중간에 브레이크포인트를 걸고 값을 확인하기가 일반 루프보다 불편해서, 디버깅 편의성과 코드 간결함 사이의 트레이드오프가 있다.

### 새 날짜/시간 API - `java.time` (JEP 150)

기존 `java.util.Date`와 `Calendar`는 가변 객체라 스레드 세이프하지 않았고, 월(month)이 0부터 시작하는 등 직관과 어긋나는 설계로 실수를 유발하기로 유명했다. JSR-310 표준을 바탕으로 `LocalDate`, `LocalDateTime`, `ZonedDateTime` 같은 불변 클래스를 도입해 이 문제를 해결했다. "가변 상태로 인한 버그를 없애기 위해 불변 객체로 다시 설계했다"는 게 이 변화의 핵심이다.

### PermGen 제거 (JEP 122)

클래스 메타데이터를 저장하던 PermGen 영역이 힙에서 완전히 빠지고, OS 네이티브 메모리를 쓰는 Metaspace로 대체됐다. PermGen은 크기를 미리 고정해야 해서 클래스를 동적으로 많이 생성하는 애플리케이션(리플렉션을 많이 쓰는 프레임워크 등)에서 `OutOfMemoryError: PermGen space`가 잦았는데, Metaspace는 기본적으로 OS가 허용하는 만큼 자동으로 늘어나 이 문제를 없앴다.

### Java 8에서 핵심은 아니지만 알아두면 좋은 것

- Nashorn JavaScript 엔진(JEP 174): JVM 안에서 자바스크립트를 실행하는 엔진으로 추가됐지만 이후 관리 부담 때문에 Java 11에서 사용 중단(deprecated) 예고, Java 15에서 완전히 제거됐다.
- 반복 가능한 어노테이션(JEP 120): 같은 어노테이션을 한 대상에 여러 번 붙일 수 있게 됐다.
- 타입 어노테이션(JEP 104, JSR 308): 변수 선언뿐 아니라 제네릭 타입 인자, 캐스팅 등 타입이 쓰이는 모든 자리에 어노테이션을 붙일 수 있게 되어, 정적 분석 도구(Checker Framework 등)가 null 가능성 같은 것을 컴파일 시점에 검사할 수 있는 기반이 됐다.

## Java 11 (2018년 9월, LTS)

새로 도입된 6개월 주기 아래에서 나온 첫 LTS다. 사이에 낀 Java 9, 10에서 쌓인 변화(모듈 시스템, `var` 등)가 여기서 실무 기준점으로 정착됐다.

### HTTP 클라이언트 표준화 (JEP 321)

Java 11 이전 표준 API는 `HttpURLConnection`뿐이었고, 이건 API가 낮은 수준이라 실무에서는 다들 Apache HttpClient나 OkHttp 같은 서드파티 라이브러리를 썼다. Java 9에서 인큐베이터(정식 API로 확정되기 전 실험 단계) 모듈로 나왔던 HTTP 클라이언트가 `java.net.http` 패키지의 정식 API로 승격되어, HTTP/1.1과 HTTP/2, WebSocket을 표준 라이브러리만으로 다룰 수 있게 됐다.

```java
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users"))
    .GET()
    .build();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
```

### Java EE·CORBA 모듈 제거 (JEP 320)

Java 9의 모듈화 작업 이후 예정돼 있던 정리로, `java.xml.bind`(JAXB), `java.xml.ws`(JAX-WS), CORBA 관련 모듈이 JDK에서 완전히 빠졌다.

> [!WARNING]
> Java 8에서 Java 11로 바로 옮기는 프로젝트가 흔히 겪는 문제다. XML을 객체로 바인딩할 때 쓰던 JAXB(`javax.xml.bind.*`)가 JDK 8까지는 기본 포함이라 별도 의존성 없이 동작했는데, JDK 11에서는 아예 빠져서 `ClassNotFoundException`이 난다. `pom.xml`이나 `build.gradle`에 `jakarta.xml.bind:jakarta.xml.bind-api`와 구현체 의존성을 직접 추가해야 한다.

### 단일 파일 소스코드 실행 (JEP 330)

`javac`로 컴파일하는 과정 없이 `java HelloWorld.java`처럼 소스 파일을 바로 실행할 수 있게 됐다. 배포용은 아니고 스크립트 작성이나 학습, 간단한 검증 코드를 빠르게 돌려볼 때 쓰는 용도다.

### Flight Recorder 오픈소스화 (JEP 328)

JVM 내부 이벤트(GC, 스레드, 메서드 호출 빈도 등)를 낮은 오버헤드로 기록하는 프로파일링 도구인 JFR(Java Flight Recorder)이, 원래는 오라클 유료 JDK에만 있던 상용 기능이었는데 OpenJDK로 오픈소스화되면서 누구나 쓸 수 있게 됐다.

### Java 11에서 핵심은 아니지만 알아두면 좋은 것

- `var`를 람다 매개변수에도 쓸 수 있게 확장(JEP 323): `var` 자체는 Java 10에서 나온 지역 변수 타입 추론 문법이고, 11에서는 `(var x, var y) -> x + y`처럼 람다 인자에도 쓸 수 있게 넓혔다. 주로 람다 매개변수에 어노테이션을 붙이고 싶을 때 필요하다.
- ZGC 실험적 도입(JEP 333): 힙 크기와 무관하게 GC 정지 시간을 10ms 이하로 유지하는 것을 목표로 한 저지연 가비지 컬렉터가 실험 단계로 처음 등장했다. Java 21에서 세대별(generational) 방식으로 발전한다.
- Nest 기반 접근 제어(JEP 181): 중첩 클래스가 바깥 클래스의 `private` 멤버에 접근할 때, 예전에는 컴파일러가 눈에 보이지 않는 브리지 메서드를 몰래 만들어 우회했는데, JVM 자체에 "같은 소스 파일에서 나온 클래스들"이라는 개념(nestmate)을 추가해 더 깔끔하게 처리하도록 바꿨다.

## Java 17 (2021년 9월, LTS)

이 버전부터 LTS 주기가 3년에서 2년으로 줄었다. Java 14~16 사이에 프리뷰로 쌓인 기능들이 대거 정식으로 확정되면서, 8에서 바로 넘어오는 프로젝트 입장에서는 사실상 여러 버전 분량의 변화를 한 번에 만나게 된다.

### 레코드와 instanceof 패턴 매칭

레코드(Records, JEP 395)와 `instanceof` 패턴 매칭(JEP 394)은 정확히는 Java 16에서 최종 확정됐다. 하지만 16이 LTS가 아니라서 실무에서는 대부분 17로 옮기며 처음 마주치므로 여기서 같이 짚는다.

레코드는 필드, 생성자, `equals`/`hashCode`/`toString`을 한 줄로 대신하는 불변 데이터 클래스 문법이다.

```java
record Point(int x, int y) {}
// 아래와 같은 클래스를 자동 생성한다: 불변 필드 x, y / 생성자 / getter(x(), y()) / equals / hashCode / toString
```

DTO나 값 객체처럼 "데이터를 그대로 담아 옮기기만 하는" 클래스에 딱 맞고, 이런 클래스에서 보일러플레이트를 줄여준다. 다만 필드를 나중에 추가로 검증하거나 상속 구조를 만들어야 하는 복잡한 도메인 객체에는 맞지 않는다.

`instanceof` 패턴 매칭은 타입 확인과 캐스팅, 변수 선언을 한 번에 처리한다.

```java
// 이전
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}
// Java 16+
if (obj instanceof String s) {
    System.out.println(s.length());
}
```

### 실드 클래스 (Sealed Classes, JEP 409)

`permits`로 특정 클래스만 상속·구현을 허용하도록 제한하는 문법이다.

```java
sealed interface Shape permits Circle, Square, Triangle {}
record Circle(double radius) implements Shape {}
record Square(double side) implements Shape {}
record Triangle(double base, double height) implements Shape {}
```

`Shape`를 구현할 수 있는 타입이 이 세 개로 고정된다는 걸 컴파일러가 알기 때문에, 나중에 도입되는 스위치 패턴 매칭에서 이 세 타입을 모두 처리했는지 컴파일 시점에 검사할 수 있다. "이 타입 계층은 여기서 끝"이라고 명시하고 싶은 경우(외부에서 마음대로 확장하면 안 되는 도메인 모델)에 쓴다.

### 강한 캡슐화 기본 적용 (JEP 403)

`sun.*` 같은 JDK 내부 API에 대한 접근을 기본적으로 막는 정책이 Java 9에서 처음 도입됐고, Java 16에서 기본값이 "허용"에서 "차단"으로 바뀌었다. Java 17은 여기서 한 발 더 나가, 우회용으로 쓰던 `--illegal-access` 옵션 자체를 무력화했다. 이제 이 옵션을 줘도 경고만 찍히고 실제로는 아무 효과가 없다.

> [!WARNING]
> 리플렉션으로 JDK 내부 클래스에 접근하던 라이브러리(오래된 버전의 바이트코드 조작 라이브러리, 일부 모킹·ORM 프레임워크)가 `InaccessibleObjectException`을 던지며 깨질 수 있다. Java 11까지는 잘 돌던 애플리케이션이 17에서 갑자기 실패한다면 이 문제일 가능성이 높다. 해결책은 문제가 된 라이브러리를 최신 버전으로 올리는 것이다. `sun.misc.Unsafe`처럼 정말 저수준 기능이 필요한 경우는 `jdk.unsupported` 모듈을 통해서는 여전히 접근할 수 있다.

### Java 17에서 핵심은 아니지만 알아두면 좋은 것

- Foreign Function & Memory API 인큐베이터 시작(JEP 412): JNI 없이 자바 코드에서 네이티브(C 등) 함수를 호출하고 힙 밖 메모리를 다루는 API의 첫 실험 버전이다. Java 22에서 정식 확정된다.
- macOS/AArch64 포트(JEP 391): 애플 실리콘(M1 이후) 맥에서 네이티브로 동작하는 JDK 빌드가 처음 지원됐다. 애플 실리콘 맥으로 개발 환경을 옮긴 사람이라면 이 버전부터가 순정 지원의 시작점이다.
- 시큐리티 매니저 제거 예고(JEP 411): 애플릿 시대에 설계된 접근 제어 메커니즘인 Security Manager가 사용 중단 예고됐다. Java 24(JEP 486)에서 아예 활성화조차 못 하게 막힌다.

## Java 21 (2023년 9월, LTS)

2년 주기가 자리잡은 뒤 나온 첫 LTS이자, 이른바 "프로젝트 룸(Loom)"의 결실인 가상 스레드가 확정된 버전이다.

### 가상 스레드 (Virtual Threads, JEP 444)

기존 `Thread`(플랫폼 스레드)는 OS 스레드 하나에 그대로 대응된다. OS 스레드는 생성 비용이 크고 기본 스택 크기만 수백 KB를 차지하기 때문에, 요청마다 스레드를 하나씩 쓰는 "스레드 퍼 리퀘스트" 방식은 동시 요청이 수천 개를 넘어가면 스레드 자체가 병목이 된다. 이 한계를 피하려고 리액티브 프로그래밍(WebFlux 등) 같은 비동기 방식을 쓰기도 하는데, 콜백이 이어지는 구조라 스택 트레이스가 끊기고 디버깅이 까다롭다는 대가가 있다.

가상 스레드는 OS 스레드와 1:1로 묶이지 않는 `Thread`다. JVM이 적은 수의 플랫폼 스레드(캐리어 스레드) 위에 훨씬 많은 가상 스레드를 얹어(M:N 스케줄링) 돌리다가, 가상 스레드가 블로킹 I/O를 호출하면 그 시점에 캐리어 스레드에서 내려놓고(unmount) 다른 가상 스레드를 그 자리에 올린다. 개발자가 쓰는 API는 기존 `Thread`, `ExecutorService`와 똑같기 때문에, 코드 스타일을 리액티브로 바꾸지 않고도 블로킹 스타일 코드 그대로 수백만 개의 동시 작업을 처리할 수 있다.

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 100_000; i++) {
        executor.submit(() -> {
            String result = callSlowExternalApi(); // 블로킹 호출이어도 OS 스레드를 점유하지 않는다
            process(result);
        });
    }
}
```

가상 스레드는 항상 데몬 스레드이고 우선순위 조정이 안 되며, 생성 비용이 거의 없다시피 하기 때문에 재사용을 위해 풀링하면 안 된다(작업마다 새로 만드는 게 정석이다).

> [!WARNING]
> `synchronized` 블록이나 메서드 안에서 블로킹 I/O를 호출하면, 가상 스레드가 캐리어 스레드에서 내려오지 못하고 그대로 눌러앉는 현상(pinning)이 생긴다. 이러면 그 캐리어 스레드는 이 작업이 끝날 때까지 다른 가상 스레드를 못 받으므로, 가상 스레드를 쓰는 의미가 없어지고 심하면 스레드 부족으로 전체가 멈출 수 있다. `synchronized` 대신 `ReentrantLock` 같은 `java.util.concurrent.locks` 계열 락으로 바꾸면 이 문제가 없다.

### 레코드 패턴 (JEP 440), 스위치 패턴 매칭 (JEP 441)

레코드의 구성 요소를 그 자리에서 분해(destructuring)해서 꺼낼 수 있다.

```java
record Point(int x, int y) {}

if (obj instanceof Point(int x, int y)) {
    System.out.println(x + y);
}
```

스위치문에도 패턴 매칭이 들어와, 타입 분기와 값 추출을 같이 표현할 수 있다.

```java
String describe(Shape shape) {
    return switch (shape) {
        case Circle c -> "반지름 " + c.radius() + "인 원";
        case Square s -> "한 변 " + s.side() + "인 정사각형";
        case Triangle t -> "밑변 " + t.base() + "인 삼각형";
    };
}
```

`Shape`가 실드 인터페이스로 선언돼 있다면, 컴파일러가 `permits`에 나열된 타입을 모두 처리했는지 검사해 `default` 없이도 컴파일이 통과한다. 실드 클래스와 스위치 패턴 매칭이 짝을 이루는 지점이다.

### 시퀀스드 컬렉션 (Sequenced Collections, JEP 431)

`List`는 순서가 있는데도 첫 번째·마지막 원소를 바로 꺼내는 메서드가 없었고(`list.get(0)`, `list.get(list.size() - 1)`처럼 우회해야 했다), `LinkedHashSet`은 순서를 보장하면서도 뒤집어서 순회할 방법이 마땅치 않는 등, 컬렉션 프레임워크 안에서 "순서 있는 컬렉션"을 다루는 API가 일관되지 않았다. `SequencedCollection`, `SequencedSet`, `SequencedMap` 인터페이스가 추가되면서 `getFirst()`, `getLast()`, `reversed()` 같은 메서드가 통일된 형태로 생겼다.

### Java 21에서 핵심은 아니지만 알아두면 좋은 것

- 스코프 값(Scoped Values) 프리뷰 시작(JEP 446): 스레드에 값을 실어 나르는 `ThreadLocal`의 대안으로, 한 번 설정하면 그 범위 안에서 불변으로 유지되는 값이다. 가상 스레드가 대량으로 생성되는 환경에서 `ThreadLocal`은 정리(clean-up)를 놓치면 메모리 누수로 이어지기 쉬운데, 스코프 값은 범위를 벗어나면 자동으로 사라지도록 설계돼 있다. Java 25에서 정식 확정된다.
- 구조화된 동시성(Structured Concurrency) 프리뷰 시작(JEP 453): 여러 개의 하위 작업(subtask)을 하나의 단위로 묶어서, 부모 작업이 끝나기 전에 모든 자식 작업이 반드시 끝나거나 취소되도록 보장하는 API다. Java 25 시점에도 다섯 번째 프리뷰로, 아직 확정되지 않았다.
- 세대별 ZGC(Generational ZGC, JEP 439): Java 11에서 실험적으로 나온 ZGC에 세대 개념(오래 산 객체와 갓 생긴 객체를 나눠 관리)을 추가해 처리량을 개선했다.

## Java 25 (2025년 9월, LTS) - 최신

2년 주기의 두 번째 결실이다. Java 21~24 사이에 여러 번 이름과 형태가 바뀌며 프리뷰를 거친 기능들이 여기서 정리됐다.

### Compact Source Files and Instance Main Methods (JEP 512)

`public class`도, `static`도, 배열 매개변수도 없이 최소한의 형태로 자바 프로그램을 시작할 수 있다.

```java
void main() {
    System.out.println("Hello, Java 25");
}
```

이 기능은 Java 21에서 "Unnamed Classes and Instance Main Methods"라는 이름의 프리뷰로 처음 나온 뒤, 22·23·24를 거치며 이름과 세부 규칙이 몇 차례 바뀌다가 25에서 지금 이름으로 확정됐다. 처음 자바를 배우는 사람이 `public static void main(String[] args)`가 왜 필요한지도 모른 채 외워서 써야 했던 진입 장벽을 낮추려는 목적이 크고, 간단한 스크립트를 짤 때도 쓸 만하다. 실무의 본격적인 애플리케이션 클래스 설계에는 영향이 없다.

### 유연한 생성자 본문 (Flexible Constructor Bodies, JEP 513)

기존에는 생성자 본문에서 `super(...)`나 `this(...)` 호출이 반드시 첫 줄이어야 해서, 부모 생성자에 넘길 인자를 검증하거나 가공하는 로직을 생성자 밖의 정적 메서드로 빼는 식으로 우회해야 했다. 이제 `super`/`this` 호출 이전에 해당 인스턴스의 필드에 접근하지 않는 범위 안에서 검증·가공 코드를 넣을 수 있다.

```java
class PositiveNumber {
    PositiveNumber(int value) {
        if (value <= 0) {
            throw new IllegalArgumentException("value는 양수여야 합니다: " + value);
        }
        // 이전 검증 로직이 여기, super() 호출 앞에 올 수 있다
        super();
    }
}
```

### 모듈 임포트 선언 (Module Import Declarations, JEP 511)

패키지를 하나씩 나열하는 대신 모듈 단위로 한 번에 임포트할 수 있다.

```java
import module java.base;
```

`java.util.*`, `java.io.*`, `java.time.*`처럼 자주 쓰는 표준 패키지를 매번 나열하는 대신 한 줄로 끝난다. 큰 애플리케이션의 핵심 로직보다는, 소스 파일 하나로 끝나는 스크립트나 학습용 코드에서 체감 효과가 크다.

### 스코프 값 정식 확정 (Scoped Values, JEP 506)

Java 21에서 프리뷰로 시작한 스코프 값이 정식 API가 됐다.

```java
static final ScopedValue<String> CURRENT_USER = ScopedValue.newInstance();

void handleRequest(String user) {
    ScopedValue.where(CURRENT_USER, user).run(() -> processRequest());
}

void processRequest() {
    System.out.println("처리 중인 사용자: " + CURRENT_USER.get());
}
```

`ThreadLocal.set()`처럼 아무 때나 값을 바꿀 수 있는 게 아니라, `where(...).run(...)`으로 감싼 범위 안에서만 값이 존재하고 그 범위를 벗어나면 자동으로 사라진다. 가변 상태를 최소화하는 방향이라 가상 스레드처럼 스레드가 대량으로 생기고 사라지는 환경에 잘 맞는다.

### 컴팩트 오브젝트 헤더 (JEP 519), 세대별 셰넌도어(Generational Shenandoah, JEP 521)

둘 다 Java 24에서 실험적 기능으로 먼저 나왔다가 25에서 정식 기능으로 승격됐다. 다만 "정식 기능"이라는 것과 "기본값"이라는 것은 다르다. 컴팩트 오브젝트 헤더는 객체 하나당 붙는 헤더 크기를 96비트에서 64비트로 줄여 메모리 사용량을 낮추는 기능인데, `-XX:+UseCompactObjectHeaders` 플래그를 직접 켜야 동작하고 기본값은 여전히 꺼짐이다. 세대별 셰넌도어도 마찬가지로 별도 설정 없이는 기존 방식이 기본으로 쓰인다.

### Java 25에서 핵심은 아니지만 알아두면 좋은 것

- 구조화된 동시성(JEP 505)이 다섯 번째 프리뷰까지 갔지만 여전히 확정되지 않았다. 문법이 자주 바뀌는 기능이라 실무 코드에 바로 쓰기보다는 동향만 지켜볼 단계다.
- 패턴에서의 원시 타입(Primitive Types in Patterns, JEP 507)도 세 번째 프리뷰 단계다. `instanceof`나 `switch` 패턴 매칭을 `int`, `double` 같은 원시 타입까지 확장하는 기능이다.
- 벡터 API(Vector API, JEP 508)는 Java 16부터 열 번째 인큐베이터를 거치고 있을 만큼 오래 끌고 있는 기능이다. SIMD 명령어를 활용한 벡터 연산을 표준 API로 제공하려는 것인데, CPU 아키텍처별로 최적화를 맞추는 일이 까다로워 확정이 계속 미뤄지고 있다.
- PEM 인코딩 API(JEP 470), 안정 값(Stable Values, JEP 502)이 새 프리뷰로 등장했다.
- 32비트 x86 포트가 제거됐다(JEP 503). 요즘 서버·개발 환경에서 32비트 x86을 쓰는 경우는 거의 없어 실무 영향은 미미하다.

## LTS는 아니지만 알아두면 좋은 버전들

| 버전 | 출시 | 핵심 내용 |
| --- | --- | --- |
| 9 | 2017-09 | 모듈 시스템(JPMS, JEP 261), jshell REPL(JEP 222), 컴팩트 문자열(JEP 254) |
| 10 | 2018-03 | 지역 변수 타입 추론 `var`(JEP 286), 6개월 릴리스 주기 공식화(JEP 322) |
| 12 | 2019-03 | 스위치 표현식 첫 프리뷰(JEP 325) |
| 13 | 2019-09 | 텍스트 블록 첫 프리뷰(JEP 355) |
| 14 | 2020-03 | 스위치 표현식 확정(JEP 361), `instanceof`/레코드 패턴 첫 프리뷰, 상세해진 NPE 메시지(JEP 358) |
| 15 | 2020-09 | 텍스트 블록 확정(JEP 378), 실드 클래스 첫 프리뷰(JEP 360) |
| 16 | 2021-03 | 레코드·`instanceof` 패턴 매칭 확정(JEP 395, 394), 강한 캡슐화 기본값이 차단으로 전환(JEP 396) |
| 18 | 2022-03 | 플랫폼 기본 인코딩이 UTF-8로 통일(JEP 400), 간이 웹 서버(JEP 408) |
| 19 | 2022-09 | 가상 스레드 첫 프리뷰(JEP 425), 레코드 패턴 첫 프리뷰(JEP 405) |
| 20 | 2023-03 | 가상 스레드·레코드 패턴 두 번째 프리뷰, 스코프 값 첫 인큐베이터(JEP 429) |
| 22 | 2024-03 | Foreign Function & Memory API 확정(JEP 454), 사용하지 않는 변수 표시 `_` 확정(JEP 456) |
| 23 | 2024-09 | Javadoc 주석에 마크다운 사용 가능(JEP 467) |
| 24 | 2025-03 | Class-File API 확정(JEP 484), 스트림 게더러(Stream Gatherers) 확정(JEP 485), 컴팩트 오브젝트 헤더·세대별 셰넌도어 실험적 첫 등장 |

이 중 두 가지는 실무 영향이 커서 표만으로 넘기기엔 아쉽다.

Java 9의 모듈 시스템(JPMS)은 `module-info.java` 파일로 패키지 간 공개 범위를 컴파일 시점에 강제하는 큰 변화였다. JDK 자체도 이때 모듈 단위로 쪼개졌고, 이게 바로 앞서 다룬 Java 11의 "JDK 내부 API 접근 차단"과 Java 17의 "강한 캡슐화 기본 적용"으로 이어지는 출발점이다. 대부분의 애플리케이션 코드는 `module-info.java` 없이도 여전히 잘 동작하지만(클래스패스 방식과 모듈 방식이 공존한다), JDK 내부 구조가 이 버전에서 근본적으로 바뀌었다는 것은 알아둘 만하다.

Java 18의 UTF-8 기본 인코딩은 지루해 보이지만 실제로 자주 겪는 버그를 없앴다. 이전에는 `new FileReader(path)`처럼 인코딩을 명시하지 않으면 OS의 기본 로케일을 따라갔는데, 이게 한국어 윈도우에서는 MS949, 리눅스 서버에서는 UTF-8인 식으로 환경마다 달랐다. 개발 환경에서는 멀쩡히 되던 파일 읽기가 배포 서버에서 한글이 깨지는 사고가 여기서 나왔다. Java 18부터는 인코딩을 명시하지 않아도 플랫폼과 무관하게 항상 UTF-8이 기본값이라 이런 문제가 사라졌다.

> [!TIP]
> 새 프로젝트의 타깃 버전을 고를 때는 가장 최근에 나온 LTS보다, 스프링 부트 등 주로 쓰는 프레임워크가 정식 지원을 선언하고 생태계 라이브러리들의 호환성이 충분히 검증된 한 단계 이전 LTS를 고르는 편이 실무에서는 더 안전하다.

## 관련 문서

- [자바-람다-동작원리와-함수형-개념](./자바-람다-동작원리와-함수형-개념.md) - Java 8 람다의 내부 동작 원리를 더 깊이 다룬 문서
