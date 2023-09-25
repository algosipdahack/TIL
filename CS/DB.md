### transaction
- SQL의 묶음
- 작업의 단위
- 데이터의 무결성을 보장하기 위함

### ACID
- Atomic : 원자성 -> All or nothing
- Consistency : 일관성 -> 데이터는 일관된 규칙에 따라 저장되어야 한다.
- Independency : 독립성 -> 각 트랜잭션은 독립적으로 실행된다.
- Durability : 지속성 -> 데이터의 결과는 영구히 결과로 남는다.

### row-level lock / record lock
- shared lock: 읽기 락
  - select ... for share
  - 동시 shared lock 가능
  - shared lock시 쓰기 락 불가
- exclusive lock : 쓰기 락
  - select ... for update
  - update
  - delete
  - shared lock,exclusive lock 둘다 불가
-> 목적 : 데이터가 변경되는 것을 방지하기 위함
** select 는 락이 아님 **


### gap lock
- between 을 활용하여 데이터 조회 시 데이터 공간 사이에 lock이 걸림
- 삽입을 방지하기 위함

### lock 해제 타이밍
- commit / rollback 될 때 해제

### consistent read
- select 연산을 할 때 특정 시점의 데이터를 읽어옴
- lock을 하지 않고 undo 연산을 이용하여 특정 시점의 데이터를 가져올 수 있음
- undo 연산 -> commit되지 않은 sql를 저장하는 공간.
** select for update, select for share연산은 undo 영역에는 lock을 걸 수 없어 실제 db의 데이터를 가져온다 **


### 트랜잭션 병행 처리 시 문제점
1. dirty read : commit되지 않은 데이터를 읽을 수 있는 문제
2. unrepeatable read : 반복적을 select할 때 값이 변경되는 문제
3. phantom read : 갑자기 값이 생성되는 문제


### 격리 레벨
1. repeatable-read
- innoDB의 default level
- consistent read가 첫 select를 날릴 시점이다
  - 따라서 select 후 다른 트랜잭션에서 값을 변경해도 값이 변하지 않음
- next-key lock을 지원해서 update/delete/insert모두 방지됨
2. read committed
- commit된 데이터만 가져오는 격리레벨
- consistency read가 select할때마다 변경된다.
- unrepeatable read현상 발생가능
3. read uncommitted
- 커밋되지 않은 데이터도 가져올 수 있는 격리레벨
- consistency read없이 그냥 DB값 그대로 가져옴
- dirty read, unrepeatable read, phantom read현상 가능

### undo / redo
redo 복구 : 이미 커밋한 트랜잭션의 수정을 재반영하는 작업


### 파티셔닝
- column을 기준으로 테이블을 나눔
- 정규화 외에도 다른 경우가 존재
  - ex) content와 같은 값은 크기가 큼 -> select로 출력하지 않아도 이미 디스크에 올라가 있어 성능에 영향을 미친다.
- 전체 데이터 필요할 시 join사용

### horizontal partitioning
- 샤딩과 다른점 : 모든 파티션을 같은 DB에 저장 / 다른 DB에 저장(샤딩) -> 부하 분산
  

### 샤딩
- row를 기준으로 테이블을 나눔
- **독립된 DB테이블**에 저장됨
- 테이블의 스키마는 그대로 유지. 
- 테이블이 커질수록 인덱스의 크기도 커진다.
  - 테이블에 읽기/쓰기가 있을 때마다 인덱스에서 처리되는 시간도 늘어난다.
- hash 기반 샤딩
  - hash함수를 통해 나온 값을 기반으로 DB 분리
  - 가장 많이 사용될 패턴에 따라 partition key 잘 정의
  - 데이터가 균등하게 분배될 수 있도록 hash funtion 정의 중요

### replication
- 데이터를 copy해서 sync를 맞추는 방식
- primary / secondary

### reference
---
https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/