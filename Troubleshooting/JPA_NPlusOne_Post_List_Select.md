# JPA N+1 Post List Select (해당 파일은 예시 템플릿 파일입니다.)


## 1. 에러 로그 및 현상

게시글 목록 조회 시 성능 저하 발생. 콘솔에 게시글 개수만큼 회원 조회 쿼리가 반복됨.

```text
Hibernate: select post0_.id as id1_0_, post0_.title as title2_0_, post0_.member_id as member_i3_0_ from post post0_
Hibernate: select member0_.id as id1_1_0_, member0_.name as name2_1_0_ from member member0_ where member0_.id=?
Hibernate: select member0_.id as id1_1_0_, member0_.name as name2_1_0_ from member member0_ where member0_.id=?
... (게시글 개수만큼 반복)
```

## 2. 발생 원인

게시글(Post) 엔티티가 회원(Member) 엔티티를 ManyToOne으로 참조하고 있음.
Spring Data JPA의 `findAll()`은 Post만 조회한 후, 루프를 돌며 각각 Member를 조회하여 N+1 문제가 발생함.

## 3. 해결 방법

Fetch Join을 사용하여 한 번의 쿼리로 Post와 Member를 모두 조회하도록 변경함.

### 수정 전

```java
// PostRepository.java
List<Post> findAll(); // N+1 발생
```

### 수정 후

```java
// PostRepository.java
@Query("select p from Post p join fetch p.member")
List<Post> findAllWithMember(); // 1번 쿼리로 해결
```

## 4. 요약 및 예방

연관 엔티티 조회 시 Fetch Join을 기본으로 고려할 것. 
성능 모니터링을 통해 반복되는 SELECT 쿼리가 발생하는지 확인해야 함.
