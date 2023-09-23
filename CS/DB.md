### row-level lock
- shared lock: 읽기 락
  - select ... for share
  - 동시 shared lock 가능
  - shared lock시 쓰기 락 불가
- exclusive lock : 쓰기 락
  - select ... for update
  - update
  - delete
  - shared lock,exclusive lock 둘다 불가

** select 는 락이 아님 **

