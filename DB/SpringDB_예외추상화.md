# Spring DB 예외 추상화: 다양한 SQLException을 하나로 통일하는 법

## 체크예외의 단점, 언체크(런타임)예외로의 전환
예로들어서 JDBC DB 라이브러리를 사용하여 Repository 계층에서 DB 접근 코드를 작성할 경우, SQLException 을 던지게 되어있다.  
해당 예외는 체크 예외로서 예전에는 체크예외가 컴파일 시점에 알 수 있어 더 좋다고 생각했지만 체크 예외에는 큰 단점이 있었다.  
바로 해당 메소드를 호출하는 곳에서 호출한 메소드와 같은 예외를 던져야 했다. 이것이 큰 단점이 되었을까?  
해당 예외가 DB 접근 기술의 종속적인 예외였다. 다시말해 해당 메소드를 호출하는 레이어에 해당 종속적인 기술들이 적용되어 누수가 발생했다.  

이 문제를 해결하고자 최상위 예외은 Execption 예외를 던진다는 경우, 서비스 계층에서 특정 예외는 잡아서 처리하고 싶을때 어떤 예외인지 알 수가 없게된다.

## 문제해결 1
위 문제를 해결하고자 DB 접근 코드를 사용한 Repository 에서 직접 try-catch 를 활용하여 메서드 밖으로 예외를 던지는 것이 아닌 잡는 것 부터 시작된다.  
```java 
        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, member.getMemberId());
            pstmt.setInt(2, member.getMoney());
            pstmt.executeUpdate();
            return member;
        } catch (SQLException e) {
            throw new MyDbException(e);
        } finally {
            close(con, pstmt, null);
        }
```
위 코드에서 SQLException 이 터지면, catch 에서 내가 만든 RuntimeSQLException 을 던져준다.  
(참고로 런타임 예외는 체크예외랑 다르게 메서드를 호출하는 곳에서 똑같이 예외를 던지지 않아도 알아서 던져준다. 즉 누수가 발생하지 않는다.)

여기서 또 문제가 발생한다. 런타임 예외로 바꾸어 줬지만, 이렇게 커스터마이징 예외를 던진다면 서비스 계층에서는 특정 구체적인 예외를 잡아 처리하는 로직을 작성하기 어렵다.  
다시말해서 위에 RuntimeException 을 던지는 것과 다를게 없어진다. (물론 뭐 레포지토리에서 로그를 남겨 문제가 생긴 이후 해당 문제만 따로 잡아 처리할 수 있지만 미리 잡아두면 좋은 예외들이 있다.)

## 문제해결 2
```java
            try {
                con = dataSource.getConnection();
                pstmt = con.prepareStatement(sql);
                pstmt.setString(1, member.getMemberId());
                pstmt.setInt(2, member.getMoney());
                pstmt.executeUpdate();
                return member;
            } catch (SQLException e) {
                //h2 db
                if (e.getErrorCode() == 23505) {
                    throw new MyDuplicateKeyException(e);
                }
                throw new MyDbException(e);
            } finally {
                JdbcUtils.closeStatement(pstmt);
                JdbcUtils.closeConnection(con);
            }
```
구체적인 예외를 잡아 로직을 작성하기 위해, Repository 에서 예외 코드에 따라 구체적인 예외를 던져준다.
이렇게 하면 Service 계층에서는 아래와 같이 ``` MyDuplicateKeyException ``` 예외가 터진 경우 특정 로직을 수행 할 수 있는 코드를 작성 할 수 있다.
```java
            try {
                repository.save(new Member(memberId, 0));
                log.info("saveId={}", memberId);
            } catch (MyDuplicateKeyException e) {
                log.info("키 중복, 복구 시도");
                String retryId = generateNewId(memberId);
                log.info("retryId={}", retryId);
                repository.save(new Member(retryId, 0));
            } catch (MyDbException e) {
                log.info("데이터 접근 계층 예외", e);
            }  
``` 
근데 이러면 또 문제가 생긴다. 수많은 예외가 있고, 그 DB 마다 같은 예외여도 다른 예외코드를 던진다. 내가 어떻게 전부 커스터마이징 예외 클래스를 만들어 처리할 것인가?  
이것을 해결하기 위해서 결국 스프링에서는 다양한 DB 예외를 언체크 예외를 상속받아 추상화하여 제공해준다. (자세한 스프링 예외 구조는 검색해보자.)
결국 정리하면 커스텀예외클래스 만드는게 귀찮아서 스프링이 만들어서 제공해준다는 소리이다.

## 문제해결 3
``` java
        catch (SQLException e) {
            throw exTranslator.translate("update", sql, e);
        } finally {
            close(con, pstmt, null);
        }
```
```throw exTranslator.translate("update", sql, e);``` 이 한줄로 "if 어떤 코드면 어떤 에러를 던지고 또 if 어쩌구" 를 반복 안해도 된다.  
그냥 exTranslator.translate() 메서드에 위와 같이 넣어주면 안에서 알아서 에러 코드를 확인하여 맞는 스프링에서 만든 DB예외 에러를 던져준다. -끝-  

다음편은 뭐 JDBC 템플릿을 이용해서 중복을 제거하는 것인데, 굳이 정리를 해야할 내용은 아닌거 같다. 추가로 템플릿패턴?은 알아볼 필요가 있을 것 같다.
해당 패턴이 JDBC 기술 발전에서 자주 사용되었다.