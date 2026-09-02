# 데이터 구조 (Data Structure)

> **"순서대로 읽으면 알아서 이해되게끔 구성하였다."**

## 폴더 구조 (4단계 철학 기반)

이 폴더는 [자료구조 개념서](./00_Core_Concept/자료구조_개념서.md)의 **4단계 정의법**에 따라 구성되어 있습니다.

| 폴더 | 단계 | 설명 |
| :--- | :--- | :--- |
| `00_Core_Concept` | - | **핵심 철학** : 4단계 정의법과 자료구조의 근본 |
| `01_Physical_Basis` | 4단계 (근본) | **물리적 기반** : 배열(Array) vs 연결(Linked) |
| `02_ADT` | 1~2단계 | **추상 자료형** : "무엇을 하는가?" (기능 명세) |
| `03_Structure` | 3~3.5단계 | **논리적 설계** : "어떻게 구성하는가?" (자료구조) |
| `04_Implementation` | 4단계 | **구현체** : "코드로 어떻게 짰는가?" (Java 등) |

## 상세 목차

### 0. 핵심 철학
- [자료구조 개념서](./00_Core_Concept/자료구조_개념서.md) - 4단계 정의법, ADT vs 자료구조 vs 구현체

### 1. 물리적 기반 (배열 vs 연결)
- [README](./01_Physical_Basis/README.md) - 배열과 연결의 핵심 차이
- *(작성 예정)* Array_Theory.md - 배열의 메모리 구조와 특성
- *(작성 예정)* LinkedList_Theory.md - 연결 리스트의 메모리 구조

### 2. 추상 자료형 (ADT)
- [Map](./02_ADT/Map.md) - Key-Value 쌍의 정의와 기능
- [Set](./02_ADT/Set.md) - 중복 없는 집합의 정의

### 3. 논리적 설계 (자료구조)
- [HashTable](./03_Structure/HashTable.md) - 해시 테이블 자료구조
- **Tree/**
  - [Tree](./03_Structure/Tree/Tree.md) - 트리 구조의 개념과 종류
  - [AdvancedTree_Overview](./03_Structure/Tree/AdvancedTree_Overview.md) - 고급 트리 간략 정리 (AVL, Red-Black, B-Tree 등)

> **참고**: 해시 함수, 충돌 해결 알고리즘(체이닝, 개방 주소법)은 [알고리즘 파트](../Algorithm/Hash/Hash.md)에 있습니다.

### 4. 구현체
- [Java_HashMap](./04_Implementation/java/Java_HashMap.md) - Java의 표준 해시 테이블 구현체
- [Java_Hashtable](./04_Implementation/java/Java_Hashtable.md) - 레거시 Hashtable 클래스 분석
- [Java_LinkedHashMap](./04_Implementation/java/Java_LinkedHashMap.md) - 순서를 기억하는 Map

## 학습 순서 권장

1. **00_Core_Concept** 먼저 읽기 → 자료구조의 본질 이해
2. **01_Physical_Basis** → 모든 자료구조의 근본(배열/연결) 파악
3. **02_ADT** → "무엇을 할 것인가?" 기능 정의
4. **03_Structure** → "어떻게 배치할 것인가?" 논리적 설계
5. **04_Implementation** → 실제 코드로 어떻게 구현되는지 확인
