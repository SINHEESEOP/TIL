# LinkedHashMap

> **"순서를 기억하는 HashMap"**

## 1. LinkedHashMap이란?

`HashMap` 은 빠르지만 순서가 뒤죽박죽이다. `LinkedHashMap` 은 이 문제를 해결하기 위해 **해시 테이블 + 이중 연결 리스트** 를 결합한 자료구조다.

* **역할** : Key-Value 쌍을 저장하면서 **삽입 순서(또는 접근 순서)**를 유지한다.
* **위상** : 순서가 중요한 상황에서 `HashMap` 대신 선택하는 구현체.

## 2. 다른 언어에서의 LinkedHashMap

| 언어 | 대응되는 자료구조 | 비고 |
| :--- | :--- | :--- |
| **Java** | `LinkedHashMap` | 삽입/접근 순서 유지 |
| **Python 3.7+** | `dict` | Python 3.7부터 삽입 순서 보장 (기본) |
| **C++** | 없음 (직접 구현 필요) | `std::map`은 정렬 순서, `unordered_map`은 순서 없음 |
| **JavaScript** | `Map` | ES6 `Map`은 삽입 순서 보장 |

## 3. HashMap과의 관계

`LinkedHashMap` 은 `HashMap` 을 **상속받아** 확장한 클래스다. 즉, HashMap의 모든 기능을 그대로 쓸 수 있으면서 순서 유지 기능이 추가된 것이다.

```java
public class LinkedHashMap<K,V> extends HashMap<K,V>
    implements Map<K,V>
```

| 구분 | HashMap | LinkedHashMap |
| :--- | :--- | :--- |
| **조회 속도** | O(1) | O(1) |
| **순서** | 보장 안 함 | **삽입 순서 보장** |
| **메모리** | 적음 | 약간 더 많음 (포인터 2개 추가) |

## 4. Java 클래스 정보

```java
package java.util;

public class LinkedHashMap<K,V> extends HashMap<K,V>
    implements Map<K,V> {
    
    // 이중 연결 리스트의 시작과 끝
    transient LinkedHashMap.Entry<K,V> head;
    transient LinkedHashMap.Entry<K,V> tail;
    
    // true면 접근 순서, false면 삽입 순서
    final boolean accessOrder;
}
```

| 항목 | 내용 |
| :--- | :--- |
| **패키지** | `java.util` |
| **도입 버전** | Java 1.4 |
| **상속** | `HashMap<K,V>` |
| **구현 인터페이스** | `Map<K,V>` |

## 5. 핵심 특징

| 특징 | 설명 |
| :--- | :--- |
| **null 허용** | Key와 Value 모두 null 가능 (HashMap과 동일) |
| **순서 보장** | **삽입 순서** 또는 **접근 순서** 유지 |
| **동기화** | 지원 안 함 (Thread-Unsafe) |
| **시간 복잡도** | Key 조회 O(1), 인덱스 접근 O(n) |

## 6. HashMap vs LinkedHashMap vs TreeMap

| 구분 | HashMap | LinkedHashMap | TreeMap |
| :--- | :--- | :--- | :--- |
| **내부 구조** | 해시 테이블 | 해시 테이블 + 이중 연결 리스트 | 레드-블랙 트리 |
| **순서** | 없음 | **삽입/접근 순서** | **Key 정렬 순서** |
| **조회 속도** | O(1) | O(1) | O(log n) |
| **언제 사용?** | 순서 불필요 | 순서 유지 필요 | Key 정렬 필요 |

## 7. 순서 유지 방식

### 삽입 순서 (기본)

데이터를 넣은 순서대로 순회할 수 있다.

```java
LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
map.put("c", 3);
map.put("a", 1);
map.put("b", 2);

// 출력: c=3, a=1, b=2 (삽입 순서)
```

### 접근 순서 (accessOrder = true)

생성자에 `accessOrder = true` 를 주면, **가장 최근에 접근(get/put)한 데이터가 맨 뒤로** 이동한다.

```java
// 세 번째 인자 true = 접근 순서 모드
LinkedHashMap<String, Integer> map = new LinkedHashMap<>(16, 0.75f, true);
map.put("a", 1);
map.put("b", 2);
map.put("c", 3);

map.get("a"); // "a"가 최근에 접근됨

// 순회 시 출력 순서: b -> c -> a (a가 맨 뒤로 이동)
```

## 8. 내부 동작 원리

### 이중 연결 리스트

각 노드가 `before`, `after` 포인터를 추가로 가지고 있어서, 모든 데이터가 **이중 연결 리스트**로 꿰어져 있다.

```
[버킷 배열]           [이중 연결 리스트]
[0] -> Node(c)        head -> (c) <-> (a) <-> (b) <- tail
[1] -> null
[2] -> Node(a)
[3] -> Node(b)
```

* **버킷 배열** : 해시값 기반으로 저장 (HashMap과 동일)
* **이중 연결 리스트** : 삽입 순서대로 연결

> **주의**: 버킷 내부의 충돌 해결용 연결 리스트(체이닝)와, 전체 삽입 순서를 유지하는 이중 연결 리스트는 **별개의 연결**이다.

### 자주 헷갈리는 부분

**Q. 15번째로 넣은 값을 가져오려면 O(1)인가?**

아니다. **O(n)** 이다. `LinkedHashMap`은 `List`처럼 인덱스로 바로 접근하는 기능이 없다. 따라서 "15번째 값"을 가져오려면, 처음부터 순서대로 15번 찾아가야 해서 O(n) 시간이 걸린다.  
즉, **Key를 모를 때는 순차 탐색 O(n)**, **Key를 알 때만 해시 기반이라 O(1)** 이다.  
정리하면, "n번째 순서의 값"을 빼오려면 비효율적이지만, "특정 키 값을 안다면" 빠르게 O(1)로 찾아올 수 있다.

## 9. LRU Cache 활용

접근 순서 모드는 **LRU(Least Recently Used) 캐시**를 만들 때 아주 유용하다. 가장 오래 사용되지 않은 데이터를 자동으로 맨 앞에 두고, 용량이 초과되면 맨 앞 데이터를 삭제하면 된다.

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxSize;

    public LRUCache(int maxSize) {
        super(16, 0.75f, true);  // accessOrder = true
        this.maxSize = maxSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxSize;  // 용량 초과 시 가장 오래된 항목 자동 삭제
    }
}

// 사용 예시
LRUCache<String, Integer> cache = new LRUCache<>(3);
cache.put("a", 1);
cache.put("b", 2);
cache.put("c", 3);
cache.put("d", 4);  // "a" 자동 삭제됨

System.out.println(cache);  // {b=2, c=3, d=4}
```

## 10. 코드 예시

### 기본 사용법

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LinkedHashMapExample {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();

        // 삽입
        map.put("banana", 2);
        map.put("apple", 1);
        map.put("cherry", 3);

        // 순회 (삽입 순서 유지)
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
        // 출력:
        // banana = 2
        // apple = 1
        // cherry = 3
    }
}
```

### 접근 순서 모드

```java
LinkedHashMap<String, Integer> map = new LinkedHashMap<>(16, 0.75f, true);

map.put("a", 1);
map.put("b", 2);
map.put("c", 3);

map.get("a");  // "a" 접근 → 맨 뒤로 이동

System.out.println(map.keySet());  // [b, c, a]
```

## 11. 직접 구현해보기 (MyLinkedHashMap)

내부 동작 원리를 이해하기 위해 간단하게 직접 만들어 볼 수 있다.

```java
public class MyLinkedHashMap<K, V> {
    static class Node<K, V> {
        K key;
        V value;
        Node<K, V> next;   // 체이닝용 (버킷 내 충돌 해결)
        Node<K, V> before, after; // 순서 유지용 (전체 데이터 연결)

        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    private static final int SIZE = 16;
    private Node<K, V>[] buckets = new Node[SIZE];
    private Node<K, V> head, tail; // 이중 연결 리스트의 시작과 끝

    private int hash(K key) {
        return (key.hashCode() & 0x7fffffff) % SIZE;
    }

    public void put(K key, V value) {
        int index = hash(key);
        Node<K, V> node = buckets[index];

        // 기존 키가 있으면 값만 업데이트
        while (node != null) {
            if (node.key.equals(key)) {
                node.value = value;
                return;
            }
            node = node.next;
        }

        // 새 노드 생성 및 버킷에 체이닝
        Node<K, V> newNode = new Node<>(key, value);
        newNode.next = buckets[index];
        buckets[index] = newNode;

        // 이중 연결 리스트에 추가 (삽입 순서 유지)
        if (tail == null) {
            head = tail = newNode;
        } else {
            tail.after = newNode;
            newNode.before = tail;
            tail = newNode;
        }
    }

    public V get(K key) {
        int index = hash(key);
        Node<K, V> node = buckets[index];
        while (node != null) {
            if (node.key.equals(key)) return node.value;
            node = node.next;
        }
        return null;
    }

    // 삽입 순서대로 출력
    public void printInsertionOrder() {
        Node<K, V> current = head;
        while (current != null) {
            System.out.println(current.key + " => " + current.value);
            current = current.after;
        }
    }
}
```

## 12. 결론

1. **정체** : HashMap을 상속받아 순서 유지 기능을 추가한 구현체.
2. **순서** : 기본은 **삽입 순서**, 옵션으로 **접근 순서** 선택 가능.
3. **성능** : Key로 찾는 건 O(1), 순서대로 N번째 값을 찾는 건 O(n).
4. **활용** : LRU Cache 구현에 매우 유용하다.

## 13. 관련 문서

* **자료구조 이론**: [HashTable (자료구조)](../../03_Structure/HashTable.md)
* **기본 구현체**: [Java_HashMap](./Java_HashMap.md)
* **레거시 클래스**: [Java_Hashtable](./Java_Hashtable.md)
