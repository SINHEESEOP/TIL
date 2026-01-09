# Set (집합) - ADT

> **"중복 없이 데이터를 모은다"는 기능 명세 (ADT)**


## 1단계 : 이런 상황이 필요했다 (Requirements)

### 상황 1: 출석 체크

```
"오늘 출석한 학생 이름을 기록하고 싶어.
 같은 학생이 두 번 체크해도 한 번만 기록되게."
```

| 출석 (입력) | 결과 (Set) |
| :--- | :--- |
| "철수", "영희", "철수", "민수" | "철수", "영희", "민수" |

배열로 하면? → 매번 "이미 있나?" 확인하고 넣어야 함. 귀찮다.  
**"중복 체크 자동으로 해줘!"**

### 상황 2: 로또 번호

```
"1~45 중에서 6개를 뽑아야 하는데,
 같은 숫자가 두 번 나오면 안 돼."
```

Set에 넣으면? → 중복이면 안 들어간다. 6개 찰 때까지 계속 넣으면 끝!

### 1단계 정리: 필요한 기능

* `add(element)` : 데이터를 넣는다. 이미 있으면 무시.
* `contains(element)` : 특정 데이터가 있는지 확인한다. (**핵심 기능**)
* `remove(element)` : 데이터를 삭제한다.

이렇게 **"중복 없이 데이터를 모은다"** 는 기능 명세가 바로 **Set ADT** 다.


## ⚠️ 이 문서의 위치

이 문서는 **02_ADT 폴더**에 있다. 즉, **Set은 자료구조가 아니라 ADT(추상 자료형)** 다.

흔히 "Set 자료구조"라고 부르지만, 내가 생각하는 자료구조 개념서의 관점에서는 **Set은 "기능 명세서(ADT)"** 에 해당하고, 이를 실제로 구현할 때 선택하는 **HashTable, Tree 등은 "자료구조"** 다.

| 단계 | 명칭 | 역할 | 예시 |
| :--- | :--- | :--- | :--- |
| **2단계 (ADT)** | Set | "중복 없이 모아라" - 기능 명세 | 이 문서 |
| **3단계 (자료구조)** | HashTable, Tree | "어떻게 저장할까?" - 논리적 설계 | [HashTable](../03_Structure/HashTable.md), [Tree](../03_Structure/Tree/Tree.md) |
| **4단계 (구현체)** | HashSet, TreeSet | 실제 코드로 구현한 클래스 | - |


## Set 이란? (ADT 정의)

**Set** 은 **중복을 허용하지 않고 데이터를 저장하는** 추상 자료형(ADT)이다.  
수학의 집합(Set) 개념을 그대로 가져온 것으로, **합집합, 교집합, 차집합** 연산도 지원한다.

### 핵심 속성

* **Unique (유일성)** : 모든 원소는 유일해야 한다. 중복 불가.
* **Unordered (무순서)** : 원소들 간에 순서가 없다. (구현체에 따라 다름)
* **Mutable (가변)** : 원소를 추가/제거할 수 있다. (불변 Set도 존재)

### 주요 연산 (ADT가 정의하는 기능)

| 연산 | 설명 |
| :--- | :--- |
| `add(element)` | 집합에 원소를 추가한다. 이미 있으면 무시. |
| `remove(element)` | 집합에서 원소를 삭제한다. |
| `contains(element)` | 특정 원소가 집합에 있는지 확인한다. **(가장 핵심)** |
| `union(Set B)` | 합집합 (A ∪ B) |
| `intersection(Set B)` | 교집합 (A ∩ B) |
| `difference(Set B)` | 차집합 (A - B) |


## Set ADT를 구현하는 방법 (자료구조 선택)

Set ADT는 **"중복 없이 빠르게 찾아라"** 라는 명세만 있을 뿐, **어떻게** 찾을지는 정하지 않는다.  
이를 구현하기 위해 선택하는 **자료구조**에 따라 성능과 특성이 달라진다.

| 자료구조 | 검색 속도 | 순서 | 대표 구현체 |
| :--- | :--- | :--- | :--- |
| **[HashTable](../03_Structure/HashTable.md)** | O(1) 평균 | 없음 | `HashSet`, `LinkedHashSet` |
| **[Tree (Red-Black)](../03_Structure/Tree/Tree.md)** | O(log n) | 값 정렬 | `TreeSet` |

> **정리**: Set(ADT) → HashTable 또는 Tree(자료구조) → HashSet, TreeSet(구현체)


## Java에서의 구현체 비교

Java는 `Set` 인터페이스를 제공하고, 내부 구현 방식에 따라 여러 클래스가 존재한다.

| 종류 | 내부 자료구조 | 순서 | 시간 복잡도 |
| :--- | :--- | :--- | :--- |
| **HashSet** | HashTable (체이닝) | 보장 안 함 | O(1) |
| **LinkedHashSet** | HashTable + LinkedList | 삽입 순서 | O(1) |
| **TreeSet** | Red-Black Tree | 값 크기 순서 | O(log n) |

### 언제 뭘 쓰는가?

* **HashSet** : 대부분의 상황에서 기본 선택. 순서가 필요 없고 빠른 조회가 목적일 때.
* **LinkedHashSet** : 삽입 순서대로 순회해야 할 때.
* **TreeSet** : 값을 정렬된 상태로 유지해야 할 때. (예: 범위 검색, 최소/최대 값 조회)

### [심화] 구현체별 상세 구조

#### ① HashSet / LinkedHashSet (해시 기반)

* **기반**: `HashMap`을 내부적으로 사용한다.
* **충돌 해결**: **체이닝(Chaining)** 방식. 기본적으로 연결 리스트, Java 8+에서는 한 버킷에 8개 이상 몰리면 **레드-블랙 트리** 로 전환.
* **LinkedHashSet**: 전체 데이터를 꿰는 **이중 연결 리스트** 가 추가되어 입력 순서를 기억한다.

#### ② TreeSet (트리 기반)

* **기반**: `TreeMap`을 내부적으로 사용한다. (해시와 무관!)
* **구조**: **레드-블랙 트리** 하나. 버킷, 해시 충돌 개념 없음.
* **저장 방식**: `hashCode()` 가 아닌 **`compare()`** 로 "작으면 왼쪽, 크면 오른쪽".


## 코드 예시

### 기본 사용법 (HashSet)

```java
import java.util.HashSet;
import java.util.Set;

public class SetExample {
    public static void main(String[] args) {
        Set<String> fruits = new HashSet<>();

        // 데이터 추가
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Apple"); // 중복! 무시됨

        System.out.println("크기: " + fruits.size()); // 2 ("Apple", "Banana")
        
        // 순서 없이 출력됨
        for (String fruit : fruits) {
            System.out.println(fruit);
        }

        // 존재 여부 확인 (빠름)
        if (fruits.contains("Banana")) {
            System.out.println("바나나 있음!");
        }
    }
}
```

### 로또 번호 생성기 (중복 제거 활용)

```java
import java.util.HashSet;
import java.util.Random;
import java.util.Set;

public class LottoGen {
    public static void main(String[] args) {
        Set<Integer> lotto = new HashSet<>();
        Random random = new Random();

        // 6개가 찰 때까지 계속 넣음 (중복이면 안 들어가니까 size가 안 늘어남)
        while (lotto.size() < 6) {
            lotto.add(random.nextInt(45) + 1);
        }

        System.out.println("로또 번호: " + lotto);
    }
}
```


## List vs Set vs Map 비교

| 특징 | List | Set | Map |
| :--- | :--- | :--- | :--- |
| **저장 단위** | 값 하나 | 값 하나 | Key-Value 쌍 |
| **중복** | 허용 | **불가** | Key 불가, Value 허용 |
| **순서** | 인덱스 있음 | 없음 (구현체 따라 다름) | 없음 (구현체 따라 다름) |
| **접근 방식** | `get(index)` | `contains(value)` | `get(key)` |


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


## [Deep Dive] Java가 체이닝(Chaining)을 고집하는 이유

Python이나 C++은 메모리 효율과 캐시 적중률이 좋은 **개방 주소법(Open Addressing)** 을 주로 쓴다.  
그런데 왜 Java는 체이닝을 쓸까?

* **삭제의 안정성**: 개방 주소법은 삭제 시 '묘비(Tombstone)' 처리가 복잡하다. Java는 GC가 발달해서 리스트 연결만 끊어버리는 게 훨씬 관리하기 편하다.
* **성능 방어**: 사용자가 `hashCode()` 를 대충 짜서 한 버킷에 데이터가 몰려도, Java 8부터 **레드-블랙 트리** 로 전환되어 최악에도 성능을 보장한다.

### 예외: IdentityHashMap (개방 주소법을 쓰는 유일한 녀석)

* **용도**: 내용이 같아도 **인스턴스(주소)가 다르면 다른 키**로 취급해야 할 때. (객체 직렬화, 깊은 복사 등)
* **왜 개방 주소법?**: `Node` 객체 생성 비용조차 아깝다고 판단. **선형 탐사(Linear Probing)** 로 쌩 배열에 직접 저장한다.


## 관련 문서

* **개념서**: [자료구조 개념서](../00_Core_Concept/자료구조_개념서.md)
* **자료구조**: [HashTable](../03_Structure/HashTable.md), [Tree](../03_Structure/Tree/Tree.md)
* **관련 ADT**: [Map](./Map.md)
