### Exclusive Lock
- 쓰기 잠금
- 어떤 트랜잭션에서 데이터를 변경하고자 할 때 해당 트랜잭션이 완료될 때까지 해당 테이블/레코드를 다른 트랜잭션에서 읽고/쓰지 못하도록 하기 위함
- 해당 락에 걸리면 Shared Lock/Exclusive Lock을 걸 수 없다

### Shared Lock
- 읽기 잠금
- 어떤 트랜잭션에서 데이터를 읽고자 할 때 다른 shared Lock은 허용, Exclusive Lock은 허용X
  - 다른 사용자가 해당 리소스를 동시에 읽을 수는 있지만, 변경은 불가능하도록 하는 것
- 동시에 여러 shared Lock -> O
- shared Lock이 하나라도 있으면 exclusive Lock은 불가