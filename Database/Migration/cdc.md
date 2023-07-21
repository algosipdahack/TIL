## CDC(Change Data Capture)
1. target DB 생성
    - source DB로 부터 생성
    - source DB와 엔진, 스키마가 다를 수 있다.
2. 초기로드
    - source DB의 snapshot을 target DB로 복사
3. CDC를 이용한 continuous Migration 수행
    - 초기 로드 이후 발생한 변경사항들은 transaction log를 통해 commit이 완료된 transaction들을 순서대로 target DB에 적용
4. 완전히 전환하기
    - 클라이언트 요청이 target DB로 전환될 시점에 source DB의 write access를 멈추고 남아있는 commited transaction을 targetDB에 적용시켜줌
    - 이후 클라이언트를 targetDB로 연결시켜준다.