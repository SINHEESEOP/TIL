# HashMap

> **"자바(Java)의 표준 해시 테이블 구현체"**

## 1. HashMap이란?

`HashMap` 은 자바 컬렉션 프레임워크(JCF)에서 **가장 대중적으로 사용되는 Map 구현체** 다.
내부적으로 **해시(Hash)** 알고리즘을 사용하여 데이터를 저장하므로, 검색과 저장이 매우 빠르다(평균 **O(1)**).

* **역할** : Key-Value 쌍을 저장하고 관리한다.
* **위상** : "자바에서 Map을 쓴다"고 하면, 특별한 이유가 없는 한 90% 이상은 `HashMap` 을 의미한다.

## 2. 다른 언어에서의 HashMap

`HashMap` 은 자바만의 독자적인 기술이 아니다. **"Key를 해싱하여 저장한다"** 는 자료구조 자체(해시 테이블)는 모든 언어에 존재하며, 이름만 다를 뿐이다.

| 언어 | 대응되는 자료구조 | 비고 |
| :--- | :--- | :--- |
| **Java** | `HashMap` | 가장 표준적인 구현체 |
| **Python** | `dict` | `{ "key": "value" }` 문법으로 사용 |
| **C++** | `std::unordered_map` | 정렬되지 않은 Map (해시 기반) |
| **JavaScript** | `Map` (또는 `Object`) | ES6부터 `Map` 객체 정식 지원 |
| **C#** | `Dictionary` | 자바의 HashMap과 사용법이 유사함 |

## 3. 핵심: "해시 테이블"과의 관계 (혼동 주의)

가장 중요하고 헷갈리기 쉬운 개념이다. 자바에는 실제로 `java.util.Hashtable` 이라는 클래스가 존재하기 때문이다.

### Q. HashMap은 내부적으로 `Hashtable` 클래스를 가져다 쓰는가?

**아니다 (No).**

`HashMap` 은 자바의 레거시 클래스인 `Hashtable` 을 상속받거나 포함하지 않는다. **완전히 새로 작성된 별개의 클래스** 다.

* **CS 개념 사용 (O)** : `HashMap` 은 컴퓨터 과학의 **"해시 테이블 자료구조 이론"** (버킷, 해시함수, 인덱싱)을 사용하여 구현되었다.
* **Legacy 클래스 사용 (X)** : 자바 1.0에 있던 `Hashtable` 클래스는 성능과 설계상 단점이 많아 쓰지 않는다.

즉, **"이론(Concept)은 같지만, 구현(Implementation)은 더 최신의 기술로 새로 했다"** 고 이해하면 된다.

## 4. Java 클래스 정보

```java
package java.util;

public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    
    transient Node<K,V>[] table;  // 내부 버킷 배열
}
```

| 항목 | 내용 |
| :--- | :--- |
| **패키지** | `java.util` |
| **도입 버전** | Java 1.2 (Collections Framework와 함께) |
| **상속** | `AbstractMap<K,V>` |
| **구현 인터페이스** | `Map<K,V>`, `Cloneable`, `Serializable` |

## 5. 핵심 특징

| 특징 | 설명 |
| :--- | :--- |
| **null 허용** | Key와 Value 모두 null 가능 (단, null Key는 하나만) |
| **순서 보장** | 없음 (삽입 순서와 무관하게 저장됨) |
| **동기화** | **지원 안 함** (Thread-Unsafe, 멀티스레드 시 주의) |
| **시간 복잡도** | 평균 O(1), 최악 O(log n) |

## 6. HashMap vs Hashtable (클래스 비교)

그렇다면 구형 `Hashtable` 과 신형 `HashMap` 은 구체적으로 무엇이 다른가?

| 비교 항목 | HashMap (신형 표준) | Hashtable (구형 레거시) |
| :--- | :--- | :--- |
| **도입 시기** | Java 1.2 | Java 1.0 |
| **동기화 (Thread-Safe)** | **지원 안 함** (빠름) | 지원함 (느림, 모든 메서드 synchronized) |
| **Null 허용** | **Key/Value 모두 허용** | 둘 다 허용 안 함 (에러 발생) |
| **보조 해시** | 사용함 (충돌 최소화) | 사용 안 함 |
| **트리화** | Java 8+ 지원 | 없음 |
| **권장 여부** | **기본적으로 사용** | 사용하지 않음 |

> **참고**: 멀티스레드 환경에서 동기화가 필요하다면 `Hashtable` 대신 `ConcurrentHashMap` 을 쓰는 것이 현대의 표준이다.

## 7. 내부 동작 원리 (Java 고유 최적화)

`HashMap` 도 기본적으로 **해시 테이블 이론(해싱, 버킷, 체이닝)** 을 그대로 따른다. 기본적인 구조는 [HashTable (자료구조)](../../03_Structure/HashTable.md)에서 설명한 것과 **동일하다**.

여기서는 **CS 이론과 다르게 Java HashMap만이 가진 특별한 최적화**에 대해서만 다룬다.

### 1) 보조 해시 함수 (Perturbation Function)

CS 이론에서는 `Key % Size` 로 인덱스를 구한다고 배운다. 하지만 단순한 나머지 연산은 충돌이 자주 날 수 있다.

`HashMap` 은 `hashCode()` 의 **상위 16비트와 하위 16비트를 XOR 연산**하여 해시값을 한 번 더 섞는다. 이를 통해 해시 충돌을 획기적으로 줄인다.

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

### 2) 트리화 (Treeify) - Java 8+ 결정적 차이

CS 이론에서 체이닝(Chaining)은 보통 "연결 리스트"를 쓴다고 배운다. 하지만 자바의 `HashMap` 은 여기서 한 발 더 나아갔다.

* **상황** : 해시 충돌이 너무 많이 발생해서, 한 버킷에 데이터가 **8개 이상** 쌓임.
* **문제** : 리스트 탐색 속도는 O(n)이라서 느려짐.
* **해결** : 리스트를 **레드-블랙 트리(Red-Black Tree)** 로 변환해버림.
* **복구** : 6개 이하로 줄어들면 다시 리스트로 돌아감.

이 덕분에 데이터가 아무리 많이 충돌해도 검색 속도가 **O(log n)** 으로 방어된다. 이는 다른 언어의 해시 테이블 구현체들과 구별되는 **Java HashMap만의 강력한 특징** 이다.

## 8. 초기 용량과 로드 팩터

`HashMap` 을 생성할 때 성능에 영향을 주는 두 가지 설정값이 있다.

| 설정 | 기본값 | 설명 |
| :--- | :--- | :--- |
| **초기 용량 (Capacity)** | 16 | 버킷 배열의 초기 크기 |
| **로드 팩터 (Load Factor)** | 0.75 | 배열 확장 기준 비율 (75% 차면 확장) |

### 리사이징 (Rehashing)

`데이터 개수 > 용량 × 로드팩터` 가 되면 배열 크기를 **2배**로 늘리고, 모든 데이터를 새 배열에 재배치한다.

```java
// 기본 생성 (capacity=16, loadFactor=0.75)
Map<String, Integer> map = new HashMap<>();

// 용량 직접 지정 (대량 데이터 시 권장)
Map<String, Integer> map = new HashMap<>(1000);

// 용량과 로드팩터 모두 지정
Map<String, Integer> map = new HashMap<>(1000, 0.5f);
```

> **팁**: 데이터 양을 미리 알 수 있다면 초기 용량을 넉넉히 잡는 것이 리사이징 비용을 줄여 성능에 유리하다.

## 9. 코드 예시

### 기본 사용법

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapExample {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        // 삽입 (Put)
        map.put("apple", 100);
        map.put("banana", 200);
        map.put("apple", 150);  // Key 중복 → 기존 값 덮어쓰기

        // 조회 (Get)
        System.out.println(map.get("apple"));  // 150

        // 기본값 조회 (없으면 기본값 반환)
        int price = map.getOrDefault("grape", 0);  // 0

        // 삭제 (Remove)
        map.remove("banana");

        // 존재 여부 확인
        boolean hasApple = map.containsKey("apple");  // true

        // 전체 순회
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
    }
}
```

### 빈도수 세기 (실무 활용)

```java
String[] words = {"apple", "banana", "apple", "orange", "banana", "apple"};
Map<String, Integer> count = new HashMap<>();

for (String word : words) {
    count.put(word, count.getOrDefault(word, 0) + 1);
}

System.out.println(count);  // {orange=1, banana=2, apple=3}
```

## 10. 결론

1. **정체** : 자바에서 Map을 쓴다면 가장 먼저 고려해야 할 표준 구현체.
2. **관계** : 다른 언어의 `dict`, `unordered_map` 과 동일한 포지션이다.
3. **구분** : CS 이론인 "해시 테이블"을 구현한 것이지, 자바의 옛날 `Hashtable` 클래스를 갖다 쓴 게 아니다.
4. **최적화** : 보조 해시 함수와 트리화(Treeify)로 충돌 상황에서도 성능을 방어한다.

## 11. 관련 문서

* **자료구조 이론**: [HashTable (자료구조)](../../03_Structure/HashTable.md)
* **해시 알고리즘**: [Hash 알고리즘](../../../Algorithm/Hash/Hash.md)
* **레거시 클래스**: [Java_Hashtable](./Java_Hashtable.md)
* **순서 유지 Map**: [Java_LinkedHashMap](./Java_LinkedHashMap.md)
