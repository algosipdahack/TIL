- 테스트에만 사용할 목적으로 사용
- testCompileClasspath / testRuntimeClassPath에만 추가됨


### 테스트 경로에 있는 클래스들을 외부로 노출시키는 법
- java-test-fixtures 플러그인 사용
  - testFixtures 사용 가능
    - 특징
      - src/testFixtures에 존재하는 클래스에서 src/main 클래스 접근가능 / src/test는 접근 불가능
      - src/test에 존재하는 클래스에서 src/testFixtures 클래스 접근 가능
  - testFixtures 클래스들을 jar에 추가시켜 준다.
- 다른 모듈에서 사용하기 위해서는 
  - testImplementation(testFixtures(project("해당모듈")))을 해야한다.
  - 사용할 테스트 클래스에서는 jar에 포함되는 해당 모듈의 src/main과 src/testFixtures에 접근 가능 / src/test는 접근 불가

### Reference
---
https://perfectacle.github.io/2022/03/13/gradle-java-test-fixtures-plugin/