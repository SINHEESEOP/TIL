## 📌 1. `MyLinkedHashMap` 구현 (삽입 순서 유지)

```java
public class MyLinkedHashMap<K, V> {
    static class Node<K, V> {
        K key;
        V value;
        Node<K, V> next;   // 체이닝용
        Node<K, V> before, after; // 순서 유지용
        Node(K key, V value) { this.key = key; this.value = value; }
    }

    private static final int SIZE = 16;
    private Node<K, V>[] buckets = new Node[SIZE];
    private Node<K, V> head, tail;

    private int hash(K key) {
        return (key.hashCode() & 0x7fffffff) % SIZE;
    }

    public void put(K key, V value) {
        int index = hash(key);
        Node<K, V> node = buckets[index];
        while (node != null) {
            if (node.key.equals(key)) {
                node.value = value;
                return;
            }
            node = node.next;
        }

        Node<K, V> newNode = new Node<>(key, value);
        newNode.next = buckets[index];
        buckets[index] = newNode;

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

    public void printInsertionOrder() {
        Node<K, V> current = head;
        while (current != null) {
            System.out.println(current.key + " => " + current.value);
            current = current.after;
        }
    }
}
```

---

## 📌 2. 주요 개념 질문 정리

### ❓ 링크드리스트 해시맵은 결국 각 버킷이 엣지로 연결된 그래프인가?

> ❌ 아니다. 해시 충돌 시 **각 버킷에 연결 리스트**로 연결되며, 그래프 구조는 아니다.

### ❓ 자바의 LinkedHashMap은 완전한 포인터 기반 연결리스트인가?

> ❌ 포인터는 없지만, **객체 참조**를 통해 이중 연결 리스트를 구현.
> 버킷은 배열로 구성되어 있지만, 순서는 참조로 유지된다.

### ❓ 순서 보장은 무슨 의미인가?

> ✅ 기본은 **삽입 순서 유지**
> 생성자 옵션에 따라 **접근 순서 유지**도 가능 (`new LinkedHashMap<>(..., true)`)

### ❓ 15번째 삽입된 값을 가져오려면 O(1)인가?

> ❌ 아니다. 순서를 기반으로 `n번째 요소`를 가져오려면 \*\*O(n)\*\*이다.
> 키 기반 조회는 여전히 **O(1)**.

---


## ✅ 접근순서 유지란?

> **LinkedHashMap은 기본적으로 “삽입 순서”를 유지하지만**,
> 설정에 따라 **“최근 접근된 순서”로 정렬되도록 변경할 수 있습니다.**

즉,

* **기본 설정**: 삽입된 순서대로 유지
* **옵션 설정**: **가장 최근에 get/put된 순서대로 정렬**

### 🔄 접근 순서 유지 (accessOrder = true)

```java
LinkedHashMap<String, Integer> map = new LinkedHashMap<>(16, 0.75f, true);
map.put("a", 1);
map.put("b", 2);
map.put("c", 3);

map.get("a"); // 👈 "a"가 최근에 접근됨

// 출력 순서: b, c, a
```

- 이 경우는 **get() 또는 put()으로 가장 최근에 접근된 항목이 뒤로 이동**합니다.
    - (근데 어차피 어떤 순서 유지던, 마지막에 put 한게 마지막에 나옴 **핵심은 마지막에 접근된 항목이 맨 뒤로 이동임**)


### 🛠 이런 접근 순서 모드는 어디에 쓰일까?

### ✅ **LRU Cache** 구현할 때

* LRU (Least Recently Used): 가장 오래 안 쓰인 데이터를 제거해야 할 때
* 접근 순서를 유지하는 `LinkedHashMap`으로 **캐시 순서 관리 가능**

```java
@Override
protected boolean removeEldestEntry(Map.Entry eldest) {
    return size() > MAX_SIZE;
}
```

나중에 궁금하면 실제 LRU 캐시 구현 예제도 보자.

---

## ✅ 결론

* `HashMap`은 빠른 조회 -> 키를 해시하여 인덱스로 사용하여 배열에 저장 -> 키만 알면 인덱스 조회이기 때문에 O(1)
* `LinkedHashMap`은 빠른 조회 + 순서 보장 -> 순서란 삽입 순서를 말하는 것이며, 실제 배열에서는 해시된 키를 기준으로 삽입하기 때문에 순서가 뒤죽박죽임.
    - 하지만 각 버킷은 노드로 되어있고, 각 노드는 Next 로 연결 되어 있기 때문에 순서가 보장됨.
    - 즉, 키로 찾는건 O(1) 이지만, 결국 순서대로 뽑는건 O(n) 이기 때문에 15번째로 삽입된 값을 뽑고 싶으면 순서대로 조회하여 O(n)  
* `MyHashMap`, `MyLinkedHashMap`을 구현해보며
  구조적 이해 및 시간 복잡도 개념을 더 깊이 있게 학습할 수 있음