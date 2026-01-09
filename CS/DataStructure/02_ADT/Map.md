# Map (맵 / 사전) - ADT

> **"Key로 Value를 찾는다"는 기능 명세 (ADT)**


## 1단계 : 이런 상황이 필요했다 (Requirements)

### 상황 1: 학생 성적 관리

```
"학번만 알면 그 학생의 성적을 바로 찾고 싶어."
```

| 학번 (Key) | 성적 (Value) |
| :--- | :--- |
| 20210001 | 95점 |
| 20210002 | 87점 |
| 20210003 | 92점 |

배열로 하면? → 학번이 20210001인데 인덱스가 뭔지 모른다. 처음부터 다 뒤져야 함.  
**"이름표(Key)만 있으면 바로 찾게 해줘!"**

### 상황 2: 영어 사전

```
"단어만 알면 그 뜻을 바로 찾고 싶어."
```

| 단어 (Key) | 뜻 (Value) |
| :--- | :--- |
| "apple" | "사과" |
| "banana" | "바나나" |
| "computer" | "컴퓨터" |

사전(Dictionary)은 **단어 → 뜻**을 찾는 구조다.  
그래서 Map을 **Dictionary**라고도 부른다!

### 1단계 정리: 필요한 기능

* `put(key, value)` : 이름표(Key)와 데이터(Value)를 함께 저장한다.
* `get(key)` : 이름표(Key)만 주면 데이터(Value)를 **바로** 돌려준다.
* `remove(key)` : 이름표(Key)로 해당 데이터를 삭제한다.

이렇게 **"Key로 Value를 찾는다"** 는 기능 명세가 바로 **Map ADT** 다.


## ⚠️ 이 문서의 위치

이 문서는 **02_ADT 폴더**에 있다. 즉, **Map은 자료구조가 아니라 ADT(추상 자료형)** 다.

흔히 "Map 자료구조"라고 부르지만, 내가 생각하는 자료구조 개념서의 관점에서는 **Map은 "기능 명세서(ADT)"** 에 해당하고, 이를 실제로 구현할 때 선택하는 **HashTable, Tree 등은 "자료구조"** 다.

| 단계 | 명칭 | 역할 | 예시 |
| :--- | :--- | :--- | :--- |
| **2단계 (ADT)** | Map | "Key로 Value를 찾아라" - 기능 명세 | 이 문서 |
| **3단계 (자료구조)** | HashTable, Tree | "어떻게 저장할까?" - 논리적 설계 | [HashTable](../03_Structure/HashTable.md), [Tree](../03_Structure/Tree/Tree.md) |
| **4단계 (구현체)** | HashMap, TreeMap | 실제 코드로 구현한 클래스 | [Java_HashMap](../04_Implementation/java/Java_HashMap.md) |


## Map 이란? (ADT 정의)

**Map** 은 **Key-Value 쌍** 을 저장하고, **Key로 Value를 O(1) 또는 O(log n)에 찾는** 추상 자료형(ADT)이다.  
다른 이름으로 **Dictionary(사전)**, **Associative Array(연관 배열)** 라고도 부른다.

### 핵심 속성

* **Key는 유일해야 한다** : 같은 Key가 두 번 들어오면, 기존 Value를 덮어쓴다.
* **Value는 중복 가능하다** : 서로 다른 Key가 같은 Value를 가질 수 있다.
* **순서는 구현체에 따라 다르다** : 보장하는 것도 있고, 안 하는 것도 있다.

### 주요 연산 (ADT가 정의하는 기능)

| 연산 | 설명 |
| :--- | :--- |
| `put(key, value)` | Key-Value 쌍을 저장한다. Key가 이미 있으면 Value를 덮어쓴다. |
| `get(key)` | Key에 해당하는 Value를 반환한다. 없으면 null. |
| `remove(key)` | Key에 해당하는 쌍을 삭제한다. |
| `containsKey(key)` | 해당 Key가 존재하는지 확인한다. |
| `keySet()` | 모든 Key의 집합(Set)을 반환한다. |


## Map ADT를 구현하는 방법 (자료구조 선택)

Map ADT는 **"Key로 Value를 빠르게 찾아라"** 라는 명세만 있을 뿐, **어떻게** 찾을지는 정하지 않는다.  
이를 구현하기 위해 선택하는 **자료구조**에 따라 성능과 특성이 달라진다.

| 자료구조 | 검색 속도 | 순서 | 대표 구현체 |
| :--- | :--- | :--- | :--- |
| **[HashTable](../03_Structure/HashTable.md)** | O(1) 평균 | 없음 | `HashMap`, `LinkedHashMap` |
| **[Tree (Red-Black)](../03_Structure/Tree/Tree.md)** | O(log n) | Key 정렬 | `TreeMap` |

> **정리**: Map(ADT) → HashTable 또는 Tree(자료구조) → HashMap, TreeMap(구현체)


## Java에서의 구현체 비교

Java는 `Map` 인터페이스를 제공하고, 내부 구현 방식에 따라 여러 클래스가 존재한다.

| 종류 | 내부 자료구조 | 순서 | 시간 복잡도 | 상세 문서 |
| :--- | :--- | :--- | :--- | :--- |
| **HashMap** | HashTable (체이닝) | 보장 안 함 | O(1) | [Java_HashMap](../04_Implementation/java/Java_HashMap.md) |
| **LinkedHashMap** | HashTable + LinkedList | 삽입 순서 | O(1) | [Java_LinkedHashMap](../04_Implementation/java/Java_LinkedHashMap.md) |
| **TreeMap** | Red-Black Tree | Key 크기 순서 | O(log n) | - |
| **Hashtable** | HashTable (체이닝) | 보장 안 함 | O(1) | [Java_Hashtable](../04_Implementation/java/Java_Hashtable.md) (레거시) |
| **ConcurrentHashMap** | 분할 잠금 HashTable | 보장 안 함 | O(1) | - |

### 언제 뭘 쓰는가?

* **HashMap** : 대부분의 상황에서 기본 선택. 순서가 필요 없고 빠른 조회가 목적일 때.
* **LinkedHashMap** : 삽입 순서대로 순회해야 할 때. 또는 LRU 캐시 구현 시.
* **TreeMap** : Key를 정렬된 상태로 유지해야 할 때. (예: 범위 검색, 최소/최대 Key 조회)


## 코드 예시

### 기본 사용법 (HashMap)

```java
import java.util.HashMap;
import java.util.Map;

public class MapExample {
    public static void main(String[] args) {
        Map<String, Integer> scores = new HashMap<>();

        // 데이터 추가
        scores.put("Alice", 90);
        scores.put("Bob", 85);
        scores.put("Alice", 95); // Key 중복! 기존 값(90)이 95로 덮어써짐

        System.out.println("Alice 점수: " + scores.get("Alice")); // 95

        // Key 존재 여부 확인
        if (scores.containsKey("Bob")) {
            System.out.println("Bob 있음!");
        }

        // 전체 순회
        for (Map.Entry<String, Integer> entry : scores.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }
    }
}
```

### 단어 빈도수 세기 (Map의 대표적 활용)

```java
import java.util.HashMap;
import java.util.Map;

public class WordCount {
    public static void main(String[] args) {
        String[] words = {"apple", "banana", "apple", "orange", "banana", "apple"};
        Map<String, Integer> count = new HashMap<>();

        for (String word : words) {
            // Key가 없으면 0, 있으면 기존 값에 +1
            count.put(word, count.getOrDefault(word, 0) + 1);
        }

        System.out.println(count); // {orange=1, banana=2, apple=3}
    }
}
```


## List vs Set vs Map 비교

| 특징 | List | Set | Map |
| :--- | :--- | :--- | :--- |
| **저장 단위** | 값 하나 | 값 하나 | **Key-Value 쌍** |
| **중복** | 허용 | 불가 | **Key 불가, Value 허용** |
| **순서** | 인덱스 있음 | 없음 (구현체 따라 다름) | 없음 (구현체 따라 다름) |
| **접근 방식** | `get(index)` | `contains(value)` | **`get(key)`** |


## [Deep Dive] Set은 Map에서 Value만 뺀 것이다

사실 **Map과 Set은 거의 같은 ADT** 다. Map에서 Value를 빼버리면 그게 바로 Set이다.

```
Map  = { Key : Value }   →  "apple" : 100,  "banana" : 200
Set  = { Key }           →  "apple",        "banana"
```

Java의 `HashSet` 은 내부적으로 `HashMap` 을 가지고 있다. 데이터를 Key로 넣고, Value에는 의미 없는 더미 객체를 채워 넣는 방식이다.

```java
// HashSet 내부 구현 (실제 Java 코드 발췌)
private static final Object PRESENT = new Object();  // 더미 객체

public boolean add(E e) {
    return map.put(e, PRESENT) == null;  // Key만 의미 있고, Value는 쓰레기
}
```

둘 다 **"중복 불가"** 라는 핵심 제약이 같기 때문에, Java는 Map을 먼저 만들고 Set은 그 위에 얹은 것이다.


## 관련 문서

* **개념서**: [자료구조 개념서](../00_Core_Concept/자료구조_개념서.md)
* **자료구조**: [HashTable](../03_Structure/HashTable.md), [Tree](../03_Structure/Tree/Tree.md)
* **구현체**: [Java_HashMap](../04_Implementation/java/Java_HashMap.md), [Java_LinkedHashMap](../04_Implementation/java/Java_LinkedHashMap.md)
