# [Java 101] ObjectComparison : 객체의 순서를 정하는 두 가지 방법

> **핵심 요약**
> * **Comparable (내장형):** 클래스 **안(In)** 에 심는다. **"기본 정렬 기준"** (딱 1개만 가능)
>   * 당연하지만 내부 구현 안되어 있으면 사용 불가로, 내부에 추가 or Comparator 방식 대표적으로 Integer 
>   같은 친구들은 구현되어 있어서 누가 더 큰지 이걸로 비교 가능.
> * **Comparator (외장형):** 클래스 **밖(Out)** 에서 만든다. **"추가 정렬 기준"** (무한대 생성 가능)
>   * 더 간단히 말하면 내가 만들어준 심판으로 비교하는 친구.
> 
> * 그럼 왜 굳이 이 둘을 인터페이스로 따로 빼두고 구현하게 했을까? 
>   * 객체지향적 관점에서 조금만 생각해도 이게 훨씬 편하기 때문이다.


## 1. 정의 및 어원 (Etymology & Definition)

객체를 정렬한다는 것은 "누가 앞이고 누가 뒤인지"를 판별하는 것이다. 자바는 이 목적을 달성하기 위해 **어근(Compare)** 은 같지만 **접미사(Suffix)** 가 다른 두 가지 인터페이스를 제공한다. 이 접미사의 차이가 곧 **역할의 차이**를 만든다.

### ① Comparable : 비교 가능한 '상태' (Adjective)

* **어원 분석:** `Compare` (비교하다) + **`-able`** (접미사: ~할 수 있는)
* **품사 변화:** 동사가 **형용사(Adjective)** 로 변했다.
* *예: `Love` + `able` = `Lovable` (사랑스러운 성질), `Eat` + `able` = `Edible` (먹을 수 있는 상태)*


* **핵심 의미:** **"나(Self)는 남과 비교될 수 있는 능력을 가졌다."**
* 객체가 태어날 때부터 **본능적(Instinct)** 으로 가지고 있는 성질을 뜻한다.
* 객체 **내부(Internal)** 에 구현되며, **"나(`this`)와 너(`o`)"** 를 비교하는 **1인칭 시점** 이다.


* **역할:** **기본 정렬 기준 (Natural Order)** (예: 주민등록번호, 학번)

### ② Comparator : 비교하는 '도구' (Noun)

* **어원 분석:** `Compare` (비교하다) + **`-or`** (접미사: ~하는 사람/기계)
* **품사 변화:** 동사가 **명사(Noun)** 로 변했다.
* *예: `Calculate` + `or` = `Calculator` (계산기), `Act` + `or` = `Actor` (배우)*


* **핵심 의미:** **"비교라는 행위를 전문적으로 수행하는 제3의 도구(Agent)다."**
* 객체의 본질과는 상관없는 **별도의 물건(Gadget)** 이다.
* 객체 **외부(External)** 에 존재하며, **"쟤(`o1`)와 얘(`o2`)"** 를 저울질하는 **3인칭 전지적 시점** 이다.


* **역할:** **특수 정렬 기준 (Strategy)** (예: 성적순, 키순, 닉네임순)

## 2. 구조 및 구현 (Structure & Implementation)

자바는 이 개념을 각각 `lang` 패키지와 `util` 패키지에 나누어 두었다.

| 구분 | Comparable | Comparator |
| :--- | :--- | :--- |
| **패키지** | `java.lang` (기초 스펙) | `java.util` (도구) |
| **메서드** | `compareTo(T o)` | `compare(T o1, T o2)` |
| **파라미터** | 1개 (나 vs 상대방) | 2개 (선수1 vs 선수2) |
| **역할** | 클래스의 **기본 스펙** 정의 | 정렬 **규칙(Rule)** 정의 |
| **비유** | 주민등록증 | 채점 기준표 |

### [코드 예시 1] Comparable : 나의 순서 정의
```java
// "학생은 학번 순서가 근본이다"
class Student implements Comparable<Student> {
    int id;
    
    @Override
    public int compareTo(Student o) {
        // 내가(this) 더 크면 양수, 작으면 음수, 같으면 0
        return this.id - o.id; 
    }
}
```

### [코드 예시 2] Comparator : 별도의 규칙 적용

```java
// "이번엔 이름 순서로 줄 서보자" (Student 클래스 수정 X)
Comparator<Student> nameComparator = new Comparator<Student>() {
    @Override
    public int compare(Student o1, Student o2) {
        return o1.name.compareTo(o2.name);
    }
};

// 사용: Collections.sort(students, nameComparator);
```

## 3. Deep Dive : 제네릭과 형변환의 이유

라이브러리나 자료구조 내부 코드를 뜯어보면 다음과 같은 패턴이 자주 등장한다.

```java
// [일반적인 비교 로직]
private int compare(E a, E b) {
    // 1. 외부 심판(Comparator)이 있으면 그걸 쓴다.
    if (comparator != null) {
        return comparator.compare(a, b);
    } 
    // 2. 심판이 없으면, 선수들끼리 알아서(Comparable) 싸워야 한다.
    else {
        // (A) 컴파일러를 위한 보증 (Casting)
        Comparable<? super E> comp = (Comparable<? super E>) a;
        // (B) 실제 비교 수행 (Runtime Overriding)
        return comp.compareTo(b);
    }
}
```

### 왜 굳이 `(Comparable)`로 캐스팅하는가?

1. **컴파일러의 시선 (Compile Time)**
* 제네릭 `E`는 소거(Erasure)되어 `Object`로 취급된다.
* `Object`에는 `compareTo()` 메서드가 없다.
* 따라서 **"야, 얘 실행할 때는 분명히 비교 가능한 놈이야!"** 라고 보증(Casting)을 서줘야 에러가 안 난다.


2. **런타임의 동작 (Run Time)**
* 실제로는 `Integer`, `String` 등 `Comparable`을 구현한 객체가 들어온다.
* 캐스팅은 문법적 허가일 뿐, 실제로는 **객체 본연의 오버라이딩된 `compareTo`** 가 실행된다. (다형성)


### 요약

> **"컴파일러를 안심시키기 위해(Casting) 가면을 씌우지만, 실제로는 알맹이(Instance)의 본능(Overriding)대로 움직인다."**
