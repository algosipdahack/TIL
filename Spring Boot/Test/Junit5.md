### JUnit 5 구조
- Jupitor, Vintage, JUnit Platform
- 상세 설명
  - Junit Platform
    - 테스트를 실행할 수 있는 엔진을 포함/ 여러 IDE에 일관성있는 API를 제공해주는 역할
  - Jupitor
    - JUnit5의 구현체
  - Vintage
    - 하위버전(4,3)들의 지원을 위한 구현체

### 테스트 프레임워크의 생성자 주입
- 테스트 프레임워크에서는 생성자 주입을 스프링 컨테이너가 아닌 Jupitor가 담당한다.
- 따라서 @Autowired를 명시해주어야 Jupitor가 Spring Container에게 빈 주입을 요청할 수 있게 된다.
  
### reference
---
https://minkukjo.github.io/framework/2020/06/28/JUnit-23/