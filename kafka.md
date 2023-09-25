producer가 커밋을 한다.

### 카프카 사용이유
- Event Driven Architecture와 Stream Data Pipeline에서 중요한 역할을 수행
  - 실시간 데이터 처리
  - 시간 단위 이벤트 데이터를 다룸


### Message Reliability
- Exactly once : 메시지가 **정확히 한번**만 전달되는 것을 뜻함. 이상적인 상황
  - 언제나 네트워크 오류와 같은 장애가 동반될 수 있다.
- At least once : 메시지가 **적어도 한번**은 전달되는 것을 의미. 
- At most once : 메시지가 **최대 한번**만 전달되는 것을 의미.
  - 데이터 유실에 tolerable 하고 성능이 중요한 서비스에 적합

### 메시지 전달 방식
- 카프카 자료구조 : log 자료 구조 -> append만 가능함.

### 문제상황 1 - Broker Ack 유실
- 네트워크 장애로 인한 Ack이 유실됨 -> Ack이 유실되는 수만큼 레코드 중복 적재

#### 해결 > 멱등성 producer

### 문제상황 2 - Consumer의 장애
- Consumer도 producer가 될 수 있는 상황이 존재
- 