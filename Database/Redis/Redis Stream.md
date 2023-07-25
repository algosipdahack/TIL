## Redis Stream이란
- append only만 되는 자료구조
- kafka와 비슷
  - consumer / consumer group 지원
  - kafka의 topic = redis stream
- redis pub/sub과 다른 점
  - 메시지가 전달 후 사라지지 않는다.
  - 다중 Consumer 존재 시 모든 consumer에게 메시지를 전달하지 않고 각각 나눠서 처리한다.

## 동작 과정
- 메시지의 id는 *로 자동 생성된다.
  - <milliseconds>-<sequenceNumber> 조합이기에 time-series저장소로도 볼 수 있다.
- 데이터 접근
  - Range를 통한 조회
    - XRANGE를 사용
    - start / end id만 있으면 조회 가능
    - COUNT 옵션을 통해 개수 제한을 두고 조회 가능
  - 구독(Subscribe) 형식으로 접근
    - XREAD를 사용
    - 모든 Consumer에게 새로운 데이터 전달
    - Consumer Group을 통해 여러 클라이언트들이 각자 다른 메시지를 가져갈 수 있음
    - Consumer Group 특징
      - 각 메시지는 각기 다른 Consumer Group에게 전달됨. 다른 Consumer Group이 같은 메시지 받을 수 없음
      - Consumer Group에 속한 Consumer들은 이름으로 구분되어야 한다.
      - Consumer가 새로운 메시지를 물어볼 때 Consumer Group은 이전에 전달된 적이 없는 메시지를 줘야 한다.
      - 메시지가 Consuming 됬다는 것을 ack을 통해 명시해준다.
      - Consumer Group은 ack되지 않은 모든 Pending중인 메시지를 찾을 수 있다.


- Redis Stream 메시지 상태 정리
  - DELIVERED : Consumer Group이 Stream으로부터 메시지를 읽어와 그룹 내 consumer에게 전달된 상태
  - PENDING : Delivered 되었지만 Consumer가 아직 메시지를 처리하지 못한 대기 상태
  - ACK : Consumer가 메시지를 처리하고 처리 완료를 명시한 상태


### 데이터 메시지 복구 처리
- Consumer가 처리에 실패하여 Pending 상태에 놓인 메시지
  - XPENDING을 통해 pending 상태의 메시지 조회 -> XCLAIM으로 메시지 재처리 지원
  - XCLAIM은 해당 message의 consumer을 바꿀 수 있다.