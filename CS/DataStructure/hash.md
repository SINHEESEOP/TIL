## ✅ 결론부터 요약

| 용어                              | 정의                                                         | 예시                           |
| ------------------------         | -----------------------------------------                   | ---------------------------- |
| **해시(Hash)**                    | **키를 고정된 크기의 숫자(인덱스)로 바꾸는 과정 전체**            | `"apple"` → 42               |
| **해시 함수(Hash Function)**      | 문자열 등을 숫자로 바꾸는 함수 (즉, 알고리즘)                     | `hash("apple") → 42`         |
| **해시 테이블(Hash Table)**        | 해시 값을 인덱스로 사용하는 **자료구조**                         | `table[hash("key")] = value` |
| **해싱(Hashing)**                 | 데이터를 해시 함수로 처리하는 **기술/과정**                      | `"key" → 해싱 → index`         |
| **해시 알고리즘(Hash Algorithm)**  | 해시 함수를 구현하는 구체적인 방식 (암호학적, 비암호학적)           | SHA-256, MD5                 |
| **해시법(Hash Method)**           | 충돌 처리까지 포함한 **해시 기반 저장법** (일본에서 자주 쓰는 표현) | 선형 조사법, 체이닝      |

---

## 📌 해시는 O(1)인가?

### ✔️ 평균 시간 복잡도: **O(1)**

* 해시 테이블은 **키를 바로 인덱스로 변환**해서 접근하기 때문에
  **삽입/검색/삭제** 모두 평균적으로 O(1).

### ❗ 하지만 최악의 경우: **O(n)**

* \*\*충돌(collision)\*\*이 많이 발생하면,
  특정 키에 여러 값이 몰려서 리스트처럼 처리됨 → 시간 복잡도 O(n)
* 그래서 **좋은 해시 함수 + 적절한 충돌 처리 방법**이 중요합니다.

---

## 🧠 각각 개념 상세 정리

### 1. 🔢 해시(Hash)

* 그냥 "무언가를 고정된 숫자로 바꾸는" 전반적인 행위 또는 개념
* 문자열 → 숫자 (인덱스)로 바꾸는 것

### 2. ⚙️ 해시 함수(Hash Function)

* 실제로 데이터를 입력받아 숫자를 반환하는 함수
* 예: `hash("dog") → 12`, `hash("cat") → 87`
* 자바의 `Object.hashCode()`, 파이썬의 `hash()` 등

### 3. 🧱 해시 테이블(Hash Table)

* **배열 기반 자료구조**
* `index = hash(key)` 로 바로 접근
* 자바의 `HashMap`, 파이썬의 `dict`, C++의 `unordered_map`

### 4. 🔄 해싱(Hashing)

* 키를 인덱스로 변환하는 기술 전반을 의미
* 예: `"userId=hong"` → 해싱 → index 42

### 5. 🧮 해시 알고리즘

* 해시 함수의 구체적인 구현
* 암호화 목적: SHA-256, SHA-512 (변조 감지용)
* 자료구조용: 나머지 연산 기반 단순 해시, DJB2, FNV 등

### 6. 📦 해시법

* 해시 충돌 처리 방법까지 포함한 **전체 저장 전략**
* 예:

  * **체이닝(Chaining)**: 같은 인덱스에 LinkedList로 연결
  * **개방 주소법(Open Addressing)**: 빈 공간 찾기 (선형, 이차, 이중 해시)

### 체이닝 자바 코드
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
### 개방 주소법
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

---

## 🧩 해시 관련 용어 관계도

```
해싱(Hashing)
 ├── 해시 함수 (Hash Function) ← 해시 알고리즘으로 구현
 ├── 해시 테이블 (Hash Table)
 └── 해시법 (Hashing Method = 해시 테이블 + 충돌 해결 방식)
```

---

## ✅ 요약

* 해시는 \*\*알고리즘(해시 함수)\*\*이자, \*\*자료구조(해시 테이블)\*\*의 핵심 기반
* **평균적으로 O(1)** 성능이 가능하지만, \*\*충돌 시 O(n)\*\*까지 떨어질 수 있음
* 용어는 문맥에 따라 다르게 쓰이므로 정확히 구분하는 것이 중요

---