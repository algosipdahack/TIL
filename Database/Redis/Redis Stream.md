## Redis Stream이란
- append only만 되는 자료구조
- 추가된 데이터는 사용자가 삭제하지 않는 한 지워지지 않음
- kafka와 비슷
  - consumer / consumer group 지원
  - kafka의 topic = redis stream
- redis pub/sub과 다른 점
  - 메시지가 전달 후 사라지지 않는다.
  - 다중 Consumer 존재 시 모든 consumer에게 메시지를 전달하지 않고 각각 나눠서 처리한다.
- Consumer Group
  - 한 stream을 여러 consumer가 분산 처리할 수 있는 방식
  - 하나의 그룹에 속한 consumer는 서로 다른 엔트리들을 조회가능

### 활용
- 센서 모니터링(지속적으로 변하는 데이터 저장 - 시간/날씨 데이터 수집)
- 유저별 알림 데이터 저장
- 이벤트 저장소

### 명령어
- XADD
  - XADD [key] [id] [field-value] : 특정 key의 stream에 엔트리 추가. stream이 없는 경우 생성
- XRANGE
  - XRANGE [key] [start] [end] : 특정 ID 범위의 엔트리를 반환
  - ex) XRANGE user-notifications - + : user-notifications의 모든 범위를 조회
- XREAD
  - XREAD BLOCK [milliseconds] STREAMS [key] [id] : 한개 이상의 key에 대해 특정 ID 이후의 엔트리를 반환
  - ex) XREAD BLOCK 0 STREAMS user-notifications 0 : user-notifications의 0보다 큰 ID 조회
- XPENDING : 처리되지 않은 메시지 확인
- XACK : 데이터 처리 완료
- XCLAIM : Pending 상태의 데이터를 다른 Consumer에게 할당
- XGROUP CREATE
  - XGROUP CREATE [key] [group name] [id] : key에 group name을 가진 consumer group을 생성
- XREADGROUP
  - XREADGROUP GROUP [group name] [consumer name] COUNT [count] STREAMS [key] [id] : 특정 key의 stream을 조회, 특정 consumer group에 속한 consumer로 읽음
  - id에 ">"를 지정하면 아직 소비되지 않은 메시지를 가져오게 된다.


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

## 가상 시나리오
- 공장의 수많은 센서로부터 메시지를 수신(Xadd)
- 센서로부터 수신된 메시지를 여러개의 워커가 DB에 저장(xgroupread)
  - xgroupread는 atomic이 보장되기 때문에 여러개의 worker가 함께 xgroupread해도 됨
- DB에 저장완료했으면 완료 처리(xack)
- 중앙 서버에서 메시지 큐에 일정 시간동안 처리 안된 메시지가 있으면 다른 워커에게 재처리 요청(XClaim)
즉, stream Key를 xgroupRead를 통해 읽어들이고, 해당 항목은 pending상태에서 ack을 받게 되면 pending 목록에서 제거되는 과정이다.


- Redis Stream 메시지 상태 정리
  - DELIVERED : Consumer Group이 Stream으로부터 메시지를 읽어와 그룹 내 consumer에게 전달된 상태
  - PENDING : Delivered 되었지만 Consumer가 아직 메시지를 처리하지 못한 대기 상태
  - ACK : Consumer가 메시지를 처리하고 처리 완료를 명시한 상태


### 데이터 메시지 복구 처리
- Consumer가 처리에 실패하여 Pending 상태에 놓인 메시지
  - XPENDING을 통해 pending 상태의 메시지 조회 -> XCLAIM으로 메시지 재처리 지원
  - XCLAIM은 해당 message의 consumer을 바꿀 수 있다.
  - 스프링에서는 스케줄러를 통해 스케쥴 된 시간마다 pending 된 메시지들을 재처리한다.
  
### Reference
---
https://kingjakeu.github.io/page2/
https://kingjakeu.github.io/springboot/2022/02/10/spring-boot-redis-stream/
https://github.com/RedisPlayGround/RedisStreams-EventDriven