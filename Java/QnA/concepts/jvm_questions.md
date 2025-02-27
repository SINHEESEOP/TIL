# JVM 관련 질문

## 질문 1: JVM의 메모리 구조에서 Metaspace가 PermGen을 대체한 이유는 무엇인가요?

### 질문 내용
Java 8에서 PermGen 영역이 Metaspace로 대체되었다고 하는데, 이러한 변경이 이루어진 이유와 두 영역의 차이점은 무엇인가요?

### 답변
Java 8에서 PermGen 영역이 Metaspace로 대체된 주요 이유는 다음과 같습니다:

1. **메모리 관리 개선**:
   - PermGen은 고정된 크기를 가지고 있어 `java.lang.OutOfMemoryError: PermGen space` 오류가 자주 발생했습니다.
   - Metaspace는 기본적으로 운영체제의 네이티브 메모리를 사용하므로 필요에 따라 자동으로 크기가 조정됩니다.

2. **클래스 로더 관련 메모리 누수 해결**:
   - PermGen에서는 클래스 로더가 언로드되지 않아 메모리 누수가 발생하는 경우가 많았습니다.
   - Metaspace는 클래스 로더가 더 이상 참조되지 않을 때 관련 메타데이터를 자동으로 해제합니다.

3. **JRockit JVM과의 통합**:
   - Oracle은 JRockit JVM과 Hotspot JVM을 통합하는 과정에서 PermGen이 없는 JRockit의 메모리 모델을 채택했습니다.

4. **성능 향상**:
   - Metaspace는 가비지 컬렉션 알고리즘이 개선되어 전체적인 성능이 향상되었습니다.

### 주요 차이점:

| 특성 | PermGen | Metaspace |
|------|---------|-----------|
| 위치 | JVM 힙 메모리 내부 | 네이티브 메모리(OS 메모리) |
| 크기 제한 | 고정 크기 (기본 64MB~82MB) | 운영체제 메모리 한도까지 확장 가능 |
| 설정 옵션 | -XX:PermSize, -XX:MaxPermSize | -XX:MetaspaceSize, -XX:MaxMetaspaceSize |
| 저장 정보 | 클래스 메타데이터, 메소드, 상수 풀 등 | 클래스 메타데이터 (상수 풀은 힙으로 이동) |
| GC 효율성 | 상대적으로 낮음 | 개선됨 |

### 관련 코드 예제
```java
// Java 7 (PermGen 사용)
// JVM 옵션: -XX:PermSize=64m -XX:MaxPermSize=128m

// Java 8 이상 (Metaspace 사용)
// JVM 옵션: -XX:MetaspaceSize=64m -XX:MaxMetaspaceSize=256m

public class MetaspaceTest {
    public static void main(String[] args) {
        // 클래스 메타데이터는 이제 Metaspace에 저장됨
        System.out.println("Metaspace 테스트");
        System.out.println("Java 버전: " + System.getProperty("java.version"));
    }
}
```

### 추가 참고 자료
- [JEP 122: Remove the Permanent Generation](https://openjdk.org/jeps/122)
- [Java 8: From PermGen to Metaspace](https://dzone.com/articles/java-8-permgen-metaspace)
- [Understanding Java Memory Model](https://www.baeldung.com/java-memory-management-interview-questions)

## 질문 2: JVM의 JIT 컴파일러는 어떤 기준으로 코드를 최적화하나요?

### 질문 내용
JVM의 JIT(Just-In-Time) 컴파일러가 바이트코드를 네이티브 코드로 변환할 때 어떤 기준과 방법으로 최적화를 수행하는지 궁금합니다.

### 답변
JIT 컴파일러는 다음과 같은 기준과 방법으로 코드를 최적화합니다:

1. **핫스팟 감지(Hotspot Detection)**:
   - JVM은 코드 실행을 모니터링하여 자주 실행되는 코드 부분(핫스팟)을 식별합니다.
   - 메소드 호출 횟수나 루프 반복 횟수 등의 임계값을 기준으로 합니다.
   - 기본적으로 메소드가 1,000~10,000번 호출되면 컴파일 대상이 됩니다.

2. **인라인화(Inlining)**:
   - 자주 호출되는 작은 메소드의 코드를 호출 지점에 직접 삽입하여 메소드 호출 오버헤드를 제거합니다.
   - 가상 메소드 호출도 런타임에 실제 호출되는 구현체가 하나로 확인되면 인라인화될 수 있습니다.

3. **루프 최적화(Loop Optimization)**:
   - 루프 언롤링(Loop Unrolling): 루프 내용을 여러 번 복제하여 반복 횟수를 줄입니다.
   - 루프 퓨전(Loop Fusion): 인접한 루프를 하나로 합칩니다.
   - 루프 불변 코드 이동(Loop-Invariant Code Motion): 루프 내에서 변하지 않는 계산을 루프 밖으로 이동합니다.

4. **탈출 분석(Escape Analysis)**:
   - 객체가 메소드 범위를 벗어나지 않는지 분석합니다.
   - 벗어나지 않는 객체는 힙 대신 스택에 할당하거나 완전히 제거할 수 있습니다.

5. **타입 특화(Type Specialization)**:
   - 제네릭 코드를 실제 사용되는 타입에 맞게 특화시킵니다.
   - 박싱/언박싱 연산을 제거합니다.

6. **분기 예측(Branch Prediction)**:
   - 조건문의 결과를 예측하여 파이프라인 최적화를 수행합니다.
   - 자주 실행되는 경로를 우선적으로 배치합니다.

7. **데드 코드 제거(Dead Code Elimination)**:
   - 실행되지 않거나 결과가 사용되지 않는 코드를 제거합니다.

8. **컴파일 수준(Compilation Levels)**:
   - C1(클라이언트) 컴파일러: 빠른 시작을 위한 기본 최적화
   - C2(서버) 컴파일러: 장기 실행을 위한 고급 최적화
   - 티어드 컴파일(Tiered Compilation): C1과 C2를 단계적으로 적용

### 관련 코드 예제
```java
// JIT 컴파일러 최적화 예시

// 인라인화 예시
public class InliningExample {
    public static void main(String[] args) {
        long sum = 0;
        // 이 루프는 JIT에 의해 최적화됨
        for (int i = 0; i < 10_000_000; i++) {
            sum += addOne(i); // 이 메소드 호출은 인라인화될 가능성이 높음
        }
        System.out.println("Sum: " + sum);
    }
    
    // 작고 자주 호출되는 메소드 - 인라인화 대상
    private static int addOne(int value) {
        return value + 1;
    }
}

// 탈출 분석 예시
public class EscapeAnalysisExample {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        
        for (int i = 0; i < 10_000_000; i++) {
            createAndProcess(); // 이 메소드 내의 객체 할당은 최적화될 수 있음
        }
        
        long end = System.currentTimeMillis();
        System.out.println("Time: " + (end - start) + "ms");
    }
    
    private static void createAndProcess() {
        // 이 객체는 메소드를 벗어나지 않으므로 스택 할당 또는 완전 제거될 수 있음
        StringBuilder sb = new StringBuilder();
        sb.append("Hello");
        sb.append(" World");
        String result = sb.toString();
        int length = result.length();
        // length 값은 사용되지 않으므로 데드 코드 제거 대상
    }
}
```

### 추가 참고 자료
- [Understanding JIT Compilation and Optimizations](https://www.baeldung.com/jvm-tiered-compilation)
- [JVM Anatomy Quark #17: Linear-Log Deopt Ticks](https://shipilev.net/jvm/anatomy-quarks/17-linear-log-deopt-ticks/)
- [Java HotSpot VM Options](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)
- [JITWatch: Analyze JIT Compiler Optimizations](https://github.com/AdoptOpenJDK/jitwatch) 