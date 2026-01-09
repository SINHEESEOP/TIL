# Java Hashtable

> **"Java 1.0의 레거시 해시 테이블 클래스 (비권장)"**

## ⚠️ 읽기 전에

**이 문서는 깊게 볼 필요 없다.**

`Hashtable`은 CS 이론의 "해시 테이블" 자료구조를 **그대로 구현한 Java 1.0 시절의 클래스**다. 즉, [HashTable (자료구조)](../../03_Structure/HashTable.md)에서 배운 내용과 **거의 동일**하며, 특별한 최적화나 Java만의 고유 기능도 없다.

**결론부터 말하면:**
- **새 코드에서는 절대 사용하지 마라.**
- **대신 `HashMap`** (단일 스레드) 또는 **`ConcurrentHashMap`** (멀티스레드)을 써라.
- 이 문서는 **레거시 코드 유지보수** 시에만 참고하면 된다.

아래는 "왜 안 쓰는지"에 대한 간략한 정리다.


## 1. 클래스 정보

```java
package java.util;

public class Hashtable<K,V> extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, Serializable
```

| 항목 | 내용 |
| :--- | :--- |
| **패키지** | `java.util` |
| **도입** | Java 1.0 (1996년) |
| **상속** | `Dictionary` (레거시 추상 클래스) |
| **정체** | 이름이 자료구조 이론과 똑같아서 혼동하기 쉬운 **유물** |

## 2. HashMap과의 비교 (핵심)

| 구분 | Hashtable (레거시) | HashMap (표준) |
| :--- | :--- | :--- |
| **스레드 안전** | O (synchronized) | X |
| **null 허용** | **X** (Key, Value 모두 불가) | O |
| **속도** | 느림 (무조건 락) | 빠름 |
| **보조 해시** | 없음 | 있음 (충돌 최소화) |
| **트리화** | 없음 | Java 8+ 지원 |
| **권장 여부** | **사용하지 않음** | **기본 선택** |

## 3. 왜 안 쓰는가? (3줄 요약)

1. **모든 메서드가 synchronized** → 단일 스레드에서도 락 비용 발생, 느림.
2. **null 불가** → 실무에서 불편.
3. **더 좋은 대체제 존재** → `HashMap`, `ConcurrentHashMap`.

## 4. 대체제 안내

| 상황 | 권장 클래스 | 링크 |
| :--- | :--- | :--- |
| **단일 스레드** | `HashMap` | [Java_HashMap](./Java_HashMap.md) |
| **멀티 스레드** | `ConcurrentHashMap` | (작성 예정) |
| **순서 필요** | `LinkedHashMap` | [Java_LinkedHashMap](./Java_LinkedHashMap.md) |

## 5. 관련 문서

* **자료구조 이론**: [HashTable (자료구조)](../../03_Structure/HashTable.md) - 내부 동작 원리는 여기서 확인
* **표준 구현체**: [Java_HashMap](./Java_HashMap.md) - 실제로 사용해야 할 클래스
