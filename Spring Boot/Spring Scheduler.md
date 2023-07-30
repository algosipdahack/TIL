### 스프링 스케쥴러
- 메소드에 @Scheduled를 붙여 사용
- Project Application Class에 @EnableScheduling추가
- 동작 방식
  - fixedRate : 작업이 rate 보다 오래 걸리면 다음 작업은 queing되고 현 작업이 종료된 후 바로 실행된다.
    - 병렬로 스케쥴러를 사용할 경우 클래스에 @EnableAsync, 메소드에 @Async 추가
    - 모든 실행이 독립적일 경우에 유용하다.
    - ex) @Scheduled(fixedRate = 30 * 1000)
  - fixedDelay : 이전 작업이 종료되면 delay만큼 지난 후 다음작업이 실행
    - 하나의 인스턴스만 항상 실행되도록 해야할 상황에 유용
    - ex) @Scheduled(fixedDelay = 30 * 1000)
    - initialDelay와 합쳐질 경우
      - initialDelay값 이후 처음 실행, fixedDelay값에 따라 계속 실행
  - cron : 이전 작업이 설정된 cron시간까지 종료되지 않으면 작업을 실행하지 않고 다음 cron 시간을 기다린다.
    - ex) @Scheduled(cron = "0,10,20,30,***")


### Reference
---
https://hongminpark.github.io/2020-04-13/Spring-Scheduler