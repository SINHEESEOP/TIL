# Set (집합)

> **"중복은 허용하지 않고, 순서는 신경 쓰지 않는 주머니"**

## 1. [CS] 자료구조로서의 Set (이론적 정의)

컴퓨터 과학(CS)에서 **Set(집합)** 은 수학적 집합 개념을 추상화한 자료구조(ADT)이다.

* **핵심 속성**
  * **Unordered**: 원소들 간에 순서가 없다. (첫 번째, 두 번째라는 개념이 없음)
  * **Unique**: 모든 원소는 유일해야 한다. (중복된 값이 존재할 수 없음)
  * **Mutable**: 원소를 추가하거나 제거할 수 있다. (단, 불변 Set도 존재)

* **주요 연산 (ADT)**
  * `add(element)`: 집합에 원소를 추가한다. (이미 있으면 무시)
  * `remove(element)`: 집합에서 원소를 삭제한다.
  * `contains(element)`: 특정 원소가 집합에 있는지 확인한다. (**가장 핵심적인 기능**)
  * `union(Set B)`: 합집합 (A ∪ B)
  * `intersection(Set B)`: 교집합 (A ∩ B)
  * `difference(Set B)`: 차집합 (A - B)


## 2. Java에서의 구현 (실전)

Java는 이 개념을 `Set` 인터페이스로 정의하고, 내부 구현 방식에 따라 여러 클래스를 제공한다.

| 종류 | 특징 | 내부 구조 (핵심) | 순서 | 시간 복잡도 |
| :--- | :--- | :--- | :--- | :--- |
| **HashSet** | 가장 빠르고 일반적임 | **Hash** + **Chaining** (리스트/트리) | 보장 안 함 | O(1) |
| **LinkedHashSet** | 넣은 순서를 기억함 | **Hash** + **Doubly LinkedList** | 입력 순서 | O(1) |
| **TreeSet** | 값이 정렬되어 저장됨 | **Red-Black Tree** (해시 안 씀) | 크기 순서 (정렬) | O(log n) |

### 💡 [심화] 구현체별 상세 구조 분석

#### ① HashSet / LinkedHashSet (해시 파)
* **기반**: `HashMap`을 내부적으로 사용한다.
* **구조**: **아파트 단지(배열/버킷)** 구조다. 해시값에 따라 몇 동(Bucket)으로 갈지 정한다.
* **충돌 해결**: **100% 체이닝(Chaining)** 방식을 쓴다. (개방 주소법 아님!)
  * 기본적으로 충돌 시 **연결 리스트(Linked List)** 로 줄줄이 매단다.
  * **Java 8+ 최적화**: 한 버킷에 데이터가 너무 많이(8개 이상) 몰리면, 자동으로 **레드-블랙 트리** 로 변신하여 성능을 방어한다.
* **LinkedHashSet**: 구조는 동일하지만, 전체 데이터를 꿰는 **빨랫줄(이중 연결 리스트)** 이 하나 더 있어서 입력 순서를 기억한다.

#### ② TreeSet (트리 파)
* **기반**: `TreeMap`을 내부적으로 사용한다. (해시(Hash)와 전혀 상관없음!)
* **구조**: 처음부터 끝까지 거대한 **이진 트리(Red-Black Tree)** 하나다.
* **특징**: **버킷(Bucket)** 이나 **해시 충돌(Collision)** 이라는 개념 자체가 없다.
* **저장 방식**: `hashCode()` 가 아닌 **`compare()`** 를 사용하여, "루트보다 작으면 왼쪽, 크면 오른쪽"으로 자기 자리를 찾아간다.


## 3. 언제 사용하는가?

* **중복 제거** : 리스트나 배열에서 중복된 값을 없애고 싶을 때.
* **존재 여부 확인** : "이 데이터가 이미 있나?"를 빠르게 체크할 때 (`contains` 속도가 매우 빠름).
* **집합 연산** : 합집합, 교집합, 차집합을 구할 때.

## 4. 코드 예시

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

## 5. List vs Set 비교

| 특징 | List (리스트) | Set (셋) |
| :--- | :--- | :--- |
| **중복** | 허용 (O) | **불가 (X)** |
| **순서** | 있음 (인덱스로 접근) | **없음 (인덱스 접근 불가)** |
| **주요 용도** | 데이터의 순서가 중요할 때 | 데이터의 고유성(Uniqueness)이 중요할 때 |

---

## [Deep Dive] Java Set의 비밀

### 1. Set은 사실 Map의 껍데기다
Java의 `HashSet` 코드를 까보면 내부에 `HashMap`을 가지고 있다.
데이터를 `Key` 로 저장하고, `Value` 에는 의미 없는 더미 객체(`PRESENT`)를 넣어 `HashMap` 의 기능(중복 Key 방지)을 그대로 이용한다.

* **왜 이렇게 했을까?**
  * **코드 재사용성**: `HashMap` 은 이미 해시 충돌 처리, 리사이징, 성능 최적화가 완벽하게 구현되어 있다.
  * **원리의 동일성**: 집합(Set)의 핵심인 **"중복 불허"** 는 Map의 **"Key 중복 불허"** 성질과 완벽하게 일치한다. 굳이 똑같은 로직을 두 번 짤 필요가 없기 때문이다.

### 2. 왜 Java는 체이닝(Chaining)을 고집할까?
Python이나 C++은 메모리 효율과 캐시 적중률(Cache Hit)이 좋은 **개방 주소법(Open Addressing, 옆 빈칸 찾기)** 을 주로 쓴다. 그런데 왜 Java는 리스트를 매다는 체이닝을 쓸까?

* **삭제(Remove)의 안정성**: 개방 주소법은 데이터 삭제 시 '묘비(Tombstone)' 처리가 복잡하다. Java는 GC(가비지 컬렉터)가 발달하여 리스트 노드 연결만 끊어버리는 것이 훨씬 관리하기 편하고 버그가 적다.
* **성능 방어**: 사용자가 `hashCode()` 를 대충 짜서 한 버킷에 데이터가 몰려도, Java 8부터는 **레드-블랙 트리** 로 전환되어 최악의 경우에도 성능을 보장한다.

### 3. 예외: 개방 주소법을 쓰는 유일한 녀석 (`IdentityHashMap`)

일반적인 개발에서는 잘 안 쓰지만, 프레임워크나 라이브러리를 뜯어보면 종종 등장하는 특이한 녀석이다.

#### ① 왜 만들어졌는가? (탄생 배경)
일반적인 `HashMap` 은 내용이 같으면(`equals`) 같은 키로 보지만, 얘는 **"내용이 똑같아도 인스턴스(주소)가 다르면 다른 키다!"** 라고 엄격하게 구분해야 할 때 쓴다.
* **사용처**: 객체 직렬화(Serialization), 깊은 복사(Deep Copy) 등 시스템 내부에서 객체의 **고유성(Identity)** 을 관리할 때 주로 쓰인다.

#### ② 왜 개방 주소법을 선택했는가?
이 녀석의 존재 목적은 **"시스템 내부용 고성능 관리"** 다.
* **체이닝(LinkedList)** 은 데이터를 넣을 때마다 `Node` 라는 객체 껍데기를 새로 만들어야 하는데, 이 메모리와 시간조차 아깝다고 판단했다.
* 그래서 **"그냥 쌩 배열 하나에 다 때려 넣자!"** 는 결론을 내렸고, 옆 칸을 뒤지는 **선형 탐사(Linear Probing)** 방식을 채택했다. (배열에 `Key`, `Value` 를 이웃하게 저장함)
