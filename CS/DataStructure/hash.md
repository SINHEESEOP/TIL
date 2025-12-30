## 핵심 요약

| 용어 | 정의 | 예시 |
| --- | --- | --- |
| **해시(Hash)** | **키를 고정된 크기의 숫자(인덱스)로 바꾸는 과정 전체** | `"apple"` → 42 |
| **해시 함수(Hash Function)** | 문자열 등을 숫자로 바꾸는 함수 (알고리즘) | `hash("apple") → 42` |
| **해시 테이블(Hash Table)** | 해시 값을 인덱스로 사용하는 **자료구조** | `table[hash("key")] = value` |
| **해싱(Hashing)** | 데이터를 해시 함수로 처리하는 **기술/과정** | `"key" → 해싱 → index` |
| **해시 알고리즘(Hash Algorithm)** | 해시 함수를 구현하는 구체적인 방식 (암호학적, 비암호학적) | SHA-256, MD5 |
| **해시법(Hash Method)** | 충돌 처리까지 포함한 **해시 기반 저장법** | 선형 조사법, 체이닝 |


## 시간 복잡도

### 평균 시간 복잡도: O(1)

* 해시 테이블은 **키를 즉시 인덱스로 변환**하여 접근하므로, **삽입/검색/삭제** 연산은 평균적으로 O(1)의 시간 복잡도를 가진다.

### 최악의 시간 복잡도: O(n)

* **해시 충돌(Hash Collision)** 이 빈번하게 발생할 경우, 특정 인덱스에 데이터가 몰리게 되어 리스트 형태처럼 동작한다.
* 이 경우 탐색 시간이 O(n)까지 저하될 수 있다.
* 따라서 **좋은 해시 함수**와 **적절한 충돌 처리 전략**이 필수적이다.


## 개념 상세 정리

### 1. 해시(Hash)

* 임의의 길이의 데이터를 고정된 길이의 데이터로 매핑하는 것.
* 일반적으로 문자열 등의 데이터를 숫자(인덱스)로 변환하는 과정을 지칭한다.

### 2. 해시 함수(Hash Function)

* 입력 데이터를 받아 해시 값(숫자)을 반환하는 함수.
* 예: `hash("dog") → 12`, `hash("cat") → 87`
* Java의 `Object.hashCode()`, Python의 `hash()` 등이 대표적인 예시이다.

### 3. 해시 테이블(Hash Table)

* **배열을 기반으로 한 자료구조**.
* `index = hash(key)` 공식을 사용하여 데이터에 직접 접근한다.
* Java의 `HashMap`, Python의 `dict`, C++의 `unordered_map` 등이 이에 해당한다.

### 4. 해싱(Hashing)

* 키(Key)를 해시 값으로 변환하고, 이를 인덱스로 활용하여 데이터를 저장하거나 검색하는 과정 전체를 의미한다.
* 예: `"userId=hong"` → 해싱 → index 42에 저장

### 5. 해시 알고리즘

* 해시 함수를 구현하는 구체적인 수학적 알고리즘.
* **암호학적 해시**: 데이터 무결성 검증 및 보안 목적 (SHA-256, SHA-512 등)
* **비암호학적 해시**: 자료구조의 빠른 검색 및 저장 목적 (단순 나머지 연산, DJB2, FNV 등)

### 6. 해시법 (Hashing Method)

해시 테이블 구조에 해시 충돌(Collision) 해결 전략을 결합한 **전체적인 데이터 저장 방식** 이다. 크게 두 가지 주류 방식이 존재한다.

#### 1) 체이닝 (Chaining)
* **방식**: 충돌 발생 시, 해당 인덱스에 **연결 리스트(LinkedList)** 를 만들어 데이터를 줄줄이 연결하여 저장한다.
* **특징**: 버킷(Bucket)이 꽉 차도 계속 데이터를 넣을 수 있다. (Java의 `HashMap`이 사용하는 방식)

#### 2) 개방 주소법 (Open Addressing)
* **방식**: 충돌 발생 시, 해시 테이블 내의 **다른 빈 공간을 찾아** 데이터를 저장한다.
* 자세한 탐색 전략(선형 탐사 등)과 삭제 메커니즘은 **[개방 주소법 상세 문서](./OpenAddressing.md)** 참조.

---

### [심화] 언어별 해시 충돌 해결 전략 비교

재미있는 점은 프로그래밍 언어마다 채택한 해시 충돌 해결 전략이 다르다는 것이다.   
각 언어의 철학에 따라 선택이 갈렸다.

| 언어 / 라이브러리 | 사용하는 방식 | 선택 이유 (철학) |
| :--- | :--- | :--- |
| **Java (HashMap)** | **체이닝 (Chaining)** | **안정성 중시**. 데이터 **삭제(remove)**가 빈번해도 처리가 깔끔하며, 리스트/트리 변환을 통해 최악의 경우에도 성능 방어가 유리하다. |
| **Python (dict)** | **개방 주소법** | **효율성 중시**. 메모리를 적게 사용하고, 데이터가 배열에 연속적으로 배치되므로 **CPU 캐시 적중률(Cache Hit)**이 높아 속도가 빠르다. |
| **Ruby, C++** | **개방 주소법** | 위와 동일한 이유 (성능 및 메모리 효율 중시). |
| **Java (ThreadLocal)** | **개방 주소법** | 예외적으로 Java 내부의 `ThreadLocalMap` 등 특수한 경우에는 개방 주소법을 사용하기도 한다. |

#### Q. 왜 Java는 개방 주소법 대신 체이닝을 선택했을까?

개방 주소법은 **데이터 삭제(Delete)** 처리가 매우 까다롭다는 단점이 있다.

1. **삭제의 딜레마**: 개방 주소법에서 데이터를 삭제하고 그 자리를 단순히 비워두면, 이후 검색 시 탐색이 중간에 끊겨버린다. (원래 데이터가 그 뒤에 있을 수 있음에도 불구하고 "없네?" 하고 멈춤)
2. **Tombstone(묘비) 관리**: 이를 해결하기 위해 "여기에 원래 데이터가 있었음"이라는 표식(Tombstone)을 남겨야 하는데, 삭제가 빈번하면 묘비가 너무 많아져 관리가 복잡해지고 성능이 저하된다.
3. **Java의 선택**: 반면 체이닝은 연결 리스트에서 노드 하나만 톡 끊어내면 되므로 삭제 연산이 매우 직관적이고 효율적이다. Java는 범용적인 사용성을 고려하여 안정적인 체이닝을 택했다.


### 구현 예제: 체이닝 (Chaining)

```java
package collection;

import java.util.LinkedList;

public class MyHashTable<K, V> {
    // 해시 버킷 배열
    private LinkedList<Node<K, V>>[] table;
    private int size;

    // 생성자
    @SuppressWarnings("unchecked")
    public MyHashTable(int capacity) {
        table = new LinkedList[capacity];
        this.size = capacity;
    }

    // 내부 노드 클래스
    private static class Node<K, V> {
        K key;
        V value;

        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    // 해시 함수: key의 해시코드를 table 크기로 나눈 나머지
    private int hash(K key) {
        return Math.abs(key.hashCode()) % size;
    }

    // 데이터 삽입
    public void put(K key, V value) {
        int index = hash(key);
        if (table[index] == null)
            table[index] = new LinkedList<>();

        for (Node<K, V> node : table[index]) {
            if (node.key.equals(key)) {
                node.value = value; // 이미 존재하면 덮어쓰기
                return;
            }
        }

        table[index].add(new Node<>(key, value)); // 새로 추가
    }

    // 데이터 조회
    public V get(K key) {
        int index = hash(key);
        if (table[index] == null) return null;

        for (Node<K, V> node : table[index]) {
            if (node.key.equals(key))
                return node.value;
        }

        return null; // 못 찾으면 null
    }

    // 데이터 삭제
    public void remove(K key) {
        int index = hash(key);
        if (table[index] == null) return;

        table[index].removeIf(node -> node.key.equals(key));
    }

    // 테스트용 출력
    public void printTable() {
        for (int i = 0; i < size; i++) {
            System.out.print("[" + i + "]: ");
            if (table[i] != null) {
                for (Node<K, V> node : table[i]) {
                    System.out.print("(" + node.key + ", " + node.value + ") ");
                }
            }
            System.out.println();
        }
    }

    // 테스트 main
    public static void main(String[] args) {
        MyHashTable<String, Integer> map = new MyHashTable<>(5);

        map.put("apple", 1);
        map.put("banana", 2);
        map.put("orange", 3);
        map.put("grape", 4);
        map.put("melon", 5);

        System.out.println("Get apple: " + map.get("apple"));
        System.out.println("Get melon: " + map.get("melon"));

        map.remove("banana");

        map.printTable();
    }
}
```

### 구현 예제: 개방 주소법 (Open Addressing)

```java
public class OpenAddressingHashTable<K, V> {
    private static class Entry<K, V> {
        K key;
        V value;
        boolean isDeleted;

        Entry(K key, V value) {
            this.key = key;
            this.value = value;
            this.isDeleted = false;
        }
    }

    private Entry<K, V>[] table;
    private int size;

    @SuppressWarnings("unchecked")
    public OpenAddressingHashTable(int capacity) {
        table = new Entry[capacity];
        this.size = capacity;
    }

    private int hash(K key) {
        return Math.abs(key.hashCode()) % size;
    }

    public void put(K key, V value) {
        int index = hash(key);
        int originalIndex = index;

        while (table[index] != null && !table[index].isDeleted && !table[index].key.equals(key)) {
            index = (index + 1) % size;
            if (index == originalIndex) throw new RuntimeException("HashTable is full");
        }

        table[index] = new Entry<>(key, value);
    }

    public V get(K key) {
        int index = hash(key);
        int originalIndex = index;

        while (table[index] != null) {
            if (!table[index].isDeleted && table[index].key.equals(key))
                return table[index].value;
            index = (index + 1) % size;
            if (index == originalIndex) break;
        }

        return null;
    }

    public void remove(K key) {
        int index = hash(key);
        int originalIndex = index;

        while (table[index] != null) {
            if (!table[index].isDeleted && table[index].key.equals(key)) {
                table[index].isDeleted = true; // 삭제 마킹
                return;
            }
            index = (index + 1) % size;
            if (index == originalIndex) break;
        }
    }

    public void printTable() {
        for (int i = 0; i < size; i++) {
            Entry<K, V> entry = table[i];
            if (entry != null && !entry.isDeleted) {
                System.out.println("[" + i + "] " + entry.key + " → " + entry.value);
            } else {
                System.out.println("[" + i + "] EMPTY");
            }
        }
    }

    public static void main(String[] args) {
        OpenAddressingHashTable<String, Integer> map = new OpenAddressingHashTable<>(7);

        map.put("apple", 1);
        map.put("banana", 2);
        map.put("grape", 3);
        map.put("melon", 4);

        System.out.println("Get banana: " + map.get("banana"));
        map.remove("banana");
        System.out.println("After removing banana: " + map.get("banana"));

        map.printTable();
    }
}
```


## 해시 관련 용어 관계도

```
해싱(Hashing)
 ├── 해시 함수 (Hash Function) ← 해시 알고리즘으로 구현
 ├── 해시 테이블 (Hash Table)
 └── 해시법 (Hashing Method = 해시 테이블 + 충돌 해결 방식)
```


## 결론

* **해시** 는 **알고리즘(해시 함수)** 이자, **자료구조(해시 테이블)** 의 핵심 기반이다.
* **평균적으로 O(1)** 의 성능을 제공하지만, **충돌 발생 시 최악의 경우 O(n)** 까지 성능이 저하될 수 있다.
* 각 용어(해시 함수, 해시 테이블, 해싱 등)는 문맥에 따라 혼용될 수 있으므로 정확한 개념 구분이 필요하다.
