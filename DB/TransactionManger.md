## 트랜잭션을 이용한 커넥션 흐름

```java
1. TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());  
    - 커넥션을 가져옴 (이때 내부적으로 동기화 매니저에 커넥션이 없는 경우, DataSource 에서 커넥션을 가져옴)
    - 가져온 커넥션에 여러 값을 설정함 (위 코드에서는 기본 디폴트 값을 설정, 당연하게 autocommit False 처리)
    - 설정이 끝난 커넥션을 트랜잭션 동기화 매니저에 바인딩함
    
2. 아래와 같은 service 로직을 실행하여 Repository 계층의 메서드 사용.
    private void bizLogic(String fromId, String toId, int money) throws SQLException {
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);

        memberRepository.update(fromId, fromMember.getMoney() - money);
        validation(toMember);
        memberRepository.update(toId, toMember.getMoney() + money);
    }

3. Repository 에서는 아래 코드를 통해서 트랜잭션 동기화 매니저에 저장된 커넥션(서비스로직에서 바인당한 커넥션)을 획득하여 수행. 
    DataSourceUtils.getConnection(dataSource);

4. 아래 코드를 통해서 마지막에 다시 동기화 매니저에 바인딩함. 직접 반환하지 않으며, 해제는 트랜잭션을 시작한 Service layer 에서 수행. 
    DataSourceUtils.releaseConnection(con, dataSource);
```

### 만약 트랜잭션을 사용하지 않는다면?
1. Service layer 에서는 그냥 서비스 로직을 호출 (트랜잭션 필요 없기 때문)
2. Repository 에서는 기존 ```DataSourceUtils.getConnection(dataSource);``` 코드를 호출 
    - 해당 코드는 동기화 매니저에 바인딩된 객체가 없다면, 직접 DataSource 에서 커넥션을 획득
    - 이후 직접 해당 커넥션을 통하여 로직 수행
3. ``` DataSourceUtils.releaseConnection(con, dataSource) ``` 코드로 직접 커넥션을 반환함. 

### transactionManager.getTransaction 가 진짜로 커넥션을 획득하나 궁금해서 확인해봄
1. JDBC 기준으로 PlatformTransactionManager 의 구현체인 DataSourceTrancationManger 클래스 확인
2. DataSourceTrancationManger 에 getTransaction 함수가 없어 부모 추상 클래스인 AbstractPlatformTransactionManager 확인
3. AbstractPlatformTransactionManager 안에 getTransaction 매서드 확인
4. 해당 메서드에서 doGetTransaction() 과, isExistingTransaction(), startTransaction() 전부 실행 되는 로직이 있는 것을 확인
5. 마지막으로 startTransaction() 에서 doBegin() 을 실행함, 해당 doBegin() 메서드의 구현체는 다시 DataSourceTrancationManger 에 있음
6. 해당 doBegin() 메서드 안에서 ``` Connection newCon = obtainDataSource().getConnection(); ``` 메서드 확인.

```
Service (@Transactional=x) 
   └─> PlatformTransactionManager.getTransaction(…)
         ├─ AbstractPlatformTransactionManager#getTransaction()   ← **여기서 시작**
         │      ├─ doGetTransaction()        (서브클래스 구현체: DataSourceTransactionManager)
         │      │     ↳ 이미 바인딩된 ConnectionHolder 조회
         │      │       (새 트랜잭션이면 아직 커넥션 없음)
         │      ├─ isExistingTransaction()   (false → 새 트랜잭션 필요)
         │      └─ startTransaction(…)       ← **여기서 doBegin() 호출**
         │             ├─ doBegin()  (DataSourceTransactionManager)
         │             │     ↳ obtainDataSource().getConnection()
         │             │        → 풀(HikariCP·DBCP 등)에서 커넥션 대여
         │             │     ↳ autoCommit=false, 격리수준 등 설정
         │             │     ↳ ConnectionHolder ThreadLocal 바인딩
         │             └─ new TransactionStatus 리턴
         └─ TransactionStatus 반환
```