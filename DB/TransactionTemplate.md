# TransactionTemplate

## 왜 만들어졌나?
PlatformTransactionManager를 직접 사용하면 **반복 코드가 너무 많다**.

### TransactionManager 직접 사용 시 문제점
```java
public void transfer(String fromId, String toId, int money) {
    TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
    try {
        // 비즈니스 로직 시작
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);
        
        memberRepository.update(fromId, fromMember.getMoney() - money);
        validation(toMember);
        memberRepository.update(toId, toMember.getMoney() + money);
        // 비즈니스 로직 끝
        
        transactionManager.commit(status);
    } catch (Exception e) {
        transactionManager.rollback(status);
        throw new IllegalStateException(e);
    }
}
```
- 트랜잭션 시작/커밋/롤백 코드가 매번 반복됨
- try-catch-finally 블록이 계속 중복됨
- 실제 비즈니스 로직보다 트랜잭션 처리 코드가 더 많음

### TransactionTemplate으로 해결
**템플릿 메서드 패턴**을 적용해서 반복 코드를 제거했다.

```java
public void transfer(String fromId, String toId, int money) {
    txTemplate.executeWithoutResult((status) -> {
        // 비즈니스 로직만 집중!
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);
        
        memberRepository.update(fromId, fromMember.getMoney() - money);
        validation(toMember);
        memberRepository.update(toId, toMember.getMoney() + money);
    });
}
```

## 언제 쓰는가?
- ```@Transactional``` 어노테이션 대신 프로그래밍 방식으로 트랜잭션을 제어하고 싶을 때
- 조건에 따라 트랜잭션을 다르게 처리해야 할 때

## 기본 사용법
```java
@Service
public class MemberService {
    private final TransactionTemplate txTemplate;
    private final MemberRepository memberRepository;
    
    public MemberService(PlatformTransactionManager txManager, MemberRepository memberRepository) {
        this.txTemplate = new TransactionTemplate(txManager);
        this.memberRepository = memberRepository;
    }
    
    public void transfer(String fromId, String toId, int money) {
        txTemplate.executeWithoutResult((status) -> {
            Member fromMember = memberRepository.findById(fromId);
            Member toMember = memberRepository.findById(toId);
            
            memberRepository.update(fromId, fromMember.getMoney() - money);
            validation(toMember);
            memberRepository.update(toId, toMember.getMoney() + money);
        });
    }
}
```

## 반환값이 있는 경우
```java
public Member findMember(String memberId) {
    return txTemplate.execute((status) -> {
        return memberRepository.findById(memberId);
    });
}
```

## 트랜잭션 설정 변경
```java
public void customTransfer(String fromId, String toId, int money) {
    TransactionTemplate template = new TransactionTemplate(txManager);
    template.setIsolationLevel(TransactionDefinition.ISOLATION_READ_COMMITTED);
    template.setTimeout(30);
    
    template.executeWithoutResult((status) -> {
        // 비즈니스 로직
    });
}
```

## 선택지 정리

### PlatformTransactionManager (직접 사용)
- 가장 기본적인 방식
- 반복 코드가 많음
- 실수하기 쉬움

### TransactionTemplate (프로그래밍 방식)
- TransactionManager의 반복 코드 문제를 해결
- 트랜잭션 적용 범위를 명확하게 제어 가능
- 조건부 트랜잭션 처리에 유리

### @Transactional (선언적)
- 가장 간편함, 코드가 깔끔
- AOP 기반이라 프록시 한계 존재

## 실무에서는?
대부분 ```@Transactional``` 을 쓰고, 복잡한 트랜잭션 제어가 필요할 때만 TransactionTemplate을 고려한다.
