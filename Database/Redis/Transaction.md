## Redis 동시성처리를 위한 Transaction
- SessionCallback 인터페이스 활용
  - Redis의 명령어로 직접 트랜잭션을 설정
    - MULTI
      - Redis의 트랜잭션을 시작
      - 이후에 입력되는 커맨드는 바로 실행되지 않고 Queue에 적재
    - EXEC
      - Queue에 쌓여있는 커맨드를 일괄적으로 실행
      - RDBMS의 commit과 비슷
    - DISCARD
      - Queue에 쌓여있는 커맨드를 일괄적으로 폐기
      - RDBMS의 rollback과 비슷
    - WATCH & UNWATCH 
      - Lock을 담당하는 커맨드, 낙관적 락을 기반
      - Watch명령어 사용 시 unwatch가 되기 전까지 한번의 exec또는 Transaction이 아닌 다른 커맨드만 허용
      - 특정 키는 트랜잭션에서 값 변경을 1번으로 제한
- @Transactional 활용
  - 필요 설정
    - @EnableTransactionManagement 적용
    - redisTemplate의 EnableTransactionSupport값 True로 설정
  - read-only 커맨드는 Queue에 들어가지 않고 바로 실행이 됨
- 주의사항
  - Transaction에서 get을 통해 값을 가져올 때 value값이 null로 return
    - multi 커맨드 이후 발생하는 커맨드는 Q ueue에 적재되기 때문에 exec구문이 끝난 뒤에 값이 return되는 것은 의미가 없기 때문에 null이 리턴됨


### Reference
---
https://wildeveloperetrain.tistory.com/137