## 이벤트 사용 이유
- 서비스 로직간의 강한 결합을 줄이기 위함
  - 여러개의 비즈니스 로직이 한번에 실행되는 경우 에러로 인한 롤백이 발생했을 경우 모든 비즈니스 로직이 롤백되는 상황 발생
  - 유지보수의 어려움, 코드 구조의 복잡성 증가
- 시간 단축
  - 다른 비즈니스 이벤트가 완료될 때까지 기다릴 필요없음

### 이벤트 구성 요소
- event class
- event publisher : 이벤트 발행
- event listener : 이벤트 수신
  
## 이벤트의 실행 단계
1. 생성 주체에서 이벤트 발생 시 이벤트 디스패처에게 전달
2. 이벤트 디스패처가 이벤트 핸들러를 연결
3. 이벤트 핸들러에서 이벤트를 실행

## 스프링 이벤트의 특징
- 멀티 캐스팅 방식으로 동작
  - 다수의 수신자가 존재하기에 동일한 타입의 여러 리스터가 등록되었다면 모든 리스너가 이벤트를 수신하게 된다.
- 동기 방식으로 동작
  - 이벤트를 발행하는 곳에서 트랜잭션이 시작되었을 때 이벤트를 수신하는 쪽과 트랜잭션이 이어진다.

### 비동기로 이벤트 처리하는 방법
- 로컬 핸들러를 비동기로 실행
  - @Async 메소드 사용
    - 이벤트를 **구독**하는 부분에 붙여준다.
    - 일부 메세지만 비동기 처리할 경우 사용
  - ApplicationEventMulticaster 사용
    - 해당 이름으로 빈 객체를 등록하는 데, 내부에서 executor을 정의해준다.
    - 세부 코드는 reference를 참조
- 메시징 큐 사용
  - event 생성 -> eventPublisher가 메시지 큐로 발행 -> 메시징 시스템 보관 -> 메시지 리스너가 이벤트 처리
- 이벤트 저장소 이용
  - polling 방식을 이용하여 이벤트가 저장되었는지 확인
  - 장점
    - 언제든지 원하는 이벤트를 가져올 수 있다. -> 이벤트 처리가 실패했을 경우에도 다시 실행 가능
  - 주의사항
    - 주요키로 자동증가 컬럼을 사용할 시
      - 자동 증가 컬럼은 insert시 증가하지만 실제 데이터는 트랜잭션을 커밋하는 시점에 DB에 반영된다.
        - 따라서 만약 DB에 반영되기 전 값을 조회했을 경우 해당 레코드가 조회되지 않을 수 있다.
      - 커밋되는 시점에 따라 자동증가 컬럼의 값이 달라질 수 있다.
    - 해결법
      - 트랜잭션 격리 레벨을 높인다.
      - 데이터 조회 시점과 ID가 증가하는 시점의 차이를 둔다.
      - CDC(Change Data Capture)을 사용한다.
        - DB 내의 데이터 변경을 식별해 필요한 후속처리를 자동화하는 기술

### 이벤트 처리와 DB 트랜잭션 고려
- 문제 발생
  - 이벤트 처리를 동기로 하던 비동기로 하던 이벤트 처리 실패와 트랜잭션 실패를 함께 고려해야 한다.
  - 이벤트는 트랜잭션과 독립적으로 실행되기 때문에 둘 중 하나의 실패는 서로에게 영향을 미치지 않기 때문이다.
- 해결책
  - @TransactionalEventListener
    - 기존 @EventListener의 경우 publisher에서 publish 한 뒤 바로 리스너가 동작함
    - 해당 어노테이션의 속성값으로 AFTER_COMMIT을 명시하면 현재 트랜잭션 커밋이 성공한 뒤에 핸들러 메서드를 실행
    ```
    @Async // 기존 트랜잭션 이후 동작을 비동기로 수행
@TransactionalEventListener(
        classes = OrderCanceledEvent.class,
        phase = TransactionPhase.AFTER_COMMIT // 기존 트랜잭션 커밋 이후 호출
)
public void handle(OrderCanceledEvent event) {
    refundService.refund(event.getOrderNumber());
}
    ```
    - 이를 통해 이벤트 핸들러를 실행했는 데 트랜잭션이 롤백되는 상황 방지 가능
    - 트랜잭션이 성공했을 때만 이벤트가 DB에 반영된다.
    - 주의사항
      - 기존 트랜잭션은 이미 커밋됐으므로 엔티티의 수정은 반영되지 않는다.
      - @Async로 새로운 스레드에서 동작하면서 다시 트랜잭션을 열어서 엔티티 작업은 가능. @Async없이는 제대로 작동안함

### 열린 내부 이벤트, 닫힌 외부 이벤트
- 내부이벤트
  - 비 관심사를 분리하여 도메인의 응집도를 높이고 비 관심사를 효율적으로 처리하는 것을 목적으로 한다.
  - 내부이벤트를 확장하여 이벤트가 구독자에게 미칠 영향을 파악하고 관리할 수 있다.
  - 외부에 알릴 필요가 없는 개념을 이벤트에 녹일 수 있다.
- 외부이벤트
  - 시스템 간의 결합을 느슨하게 만들기 위해 발행한다.
  - 해당 이벤트의 부가 정보를 전달하기 위해서는 ZERO-PAYLOAD방식을 이용
    - ID, 몇가지 정보만 보낸 뒤 수신하는 쪽에서 따로 API를 호출하여 나머지 정보를 얻어내는 방식

### 이벤트 저장소 구축 이유
- 이벤트 유실에 대한 보장
  - 이벤트를 발행하는 과정에서 유실될 수 있기 때문
- 이벤트 재발행
  - 이벤트가 잘못 처리될 수 있기 때문에 언제든 이벤트를 재발행할 수 있어야 한다.
- 모든 도메인 이벤트는 이벤트 저장소에 저장되어야 한다.
  - 트랜잭션 범위 내에서 처리하는 구독자가 필요
  - 이벤트 저장소에 저장이 실패할 시 기존 로직도 실패

### 이벤트 저장 데이터 형태
```
create table member_event
(
    id            varchar(128) not null primary key,
    published     tinyint      not null,
    published_at  datetime     null,
    created_at    datetime     not null
);

create index ix_member_event_created_at_published
    on member_event (created_at, published);
```
이벤트 발행에 대한 여부를 알 수 있도록 발행 여부 플래그가 필요하다.

### 스프링 이벤트 장점과 단점
- 장점
  - 결합도 낮춤
  - 단위 테스트의 용이성
  - 별도의 서비스로 분리 가능
  - 클래스의 재사용성 높임
- 단점
  - 전반적인 작업량 증가
  - 특정 프레임워크 API에 의존
    - 전체적인 이벤트 구독/발행 과정을 테스트하기 어려움
- 사용예시
  - 여러 도메인에서 공통으로 사용되는 경우
    - ex) 알림발송, 메일 발송
  - 순서 보장 없이 작업들을 처리해야 하는 경우

## Reference
---
https://mangkyu.tistory.com/292
https://velog.io/@backtony/Spring-Event-Driven
https://findstar.pe.kr/2022/09/17/points-to-consider-when-using-the-Spring-Events-feature/
https://velog.io/@ekxk1234/Redis-Streams%EC%99%80-MSA-Event-Driven-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98