### 분산락이란?
- 멀티 인스턴스 환경에서도 공통된 락을 사용할 수 있게끔 하는 락


### Redis의 분산락 종류
- Lettuce
  - setnx, setex등을 이용하여 분산락을 직접 구현해야 한다.
  - retry, timeout과 같은 기능을 구현해주어야 한다.
  - 스핀락으로 동작 > Redis가 받는 부하가 커짐
  
- Redisson
  - 별도의 Lock Interface를 지원
  - 타임아웃 설정이 가능하여 락을 안전하게 사용 가능
  - Pub/Sub방식을 이용 > 부하 줄임
  - wait time : 락 획득을 위해 기다리는 시간
  - lease time : 락을 임대하는 시간
  - **주의**
    - 트랜잭션 커밋 이후 락이 해제되어야 한다.
    - 동시성 환경에서 데이터의 정합성을 보장하기 위해
  - 분산 락 내부의 전파속성 : Requires_new
    - mysql의 기본 격리수준이 repeatable_read이기에 최신 커밋된 정보만 가져올 수 있다.
    - 그렇기에 커밋도 되기 전에 다른 트랜잭션이 들어오게 되면 데이터의 정합성이 깨지게 된다.
    - 따라서 분산락 내부에서는 항상 새로운 트랜잭션을 생성해야 한다.


### Reference
---
https://helloworld.kurly.com/blog/distributed-redisson-lock/
https://devnm.tistory.com/37