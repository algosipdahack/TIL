### 장점
- github와 통일된 환경에서 ci 수행이 가능
- 프로젝트마다 개별 Runner를 통한 빌드 테스트가 가능
- 친숙한 yml파일로 파이프라인 고성이 간단

### 구성요소
- workflow
  - 한개 이상의 job을 실행할 수 있는 자동화된 작업
  - event에 의해서 실행
- event
  - workflow를 실행시키는 특정활동
  - 깃허브에서 발생하는 대부분의 작업을 event로 정의 가능(pull request event)
- jobs
  - 한가지 runner안에서 실행되는 여러 step의모음
  - 병렬 실행이 가능
  - step은 순서에 따라 실행