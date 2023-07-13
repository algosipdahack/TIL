### classpath
- 클래스나 jar파일이 존재하는 위치
- complie-time classpath
  - 에러없이 컴파일을 하기위해 필요한 클래스들과 jar들의 위치
- run-time classpath
  - 애플리케이션이 정상적으로 실행하기 위해 필요한 클래스들과 jar의 경로


### 종류
- complieOnly: compileClassPath에만 추가하겠다는 의미
  - ex) lombok
- runtimeOnly: runtimeClassPath에만 추가하겠다는 의미
  - DB나 로그관련 dependency
- implementaion : 의존 라이브러리 수정 시 본 모듈까지만 재빌드
- api : 의존 라이브러리 수정 시 본 모듈을 의존하는 모듈들도 재빌드