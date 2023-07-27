## Autoconfiguration
- 라이브러리의 디펜던시를 추가해주면 빈 설정과 생성을 자동으로 해주는 편의 기능
- ex ) redis와 연결된 스프링 애플리케이션을 만들고 싶을 때 spring-boot-starter-data-redis를 의존성에 추가해주면 redisTemplate 빈과 연결 설정이 가능
- 별도의 설정이 없는 경우 127.0.0.1:6379로 연결을 시도
  - host/port는 application.yml에 설정하면 자동으로 읽어들인다.

### Reference
---
https://brunch.co.kr/@anonymdevoo/49