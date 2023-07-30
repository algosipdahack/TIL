### @Modifying
- @Query(JPQL Query, Native Query)를 통해 작성된 INSERT, UPDATE, DELETE쿼리에서 사용되는 어노테이션
- 기본적으로 JPARepository에서 제공하는 메서드로 만들어진 쿼리에는 적용되지 않는다.
- 주로 벌크 연산과 함께 이용된다.
- clearAutomatically옵션
  - 해당 옵션값을 true로 넘겨주게 되면 벌크 연산 직후 자동으로 영속성 컨텍스트를 clear해준다.
- flushAutomatically
  - 해당 쿼리를 실행하기 전, 영속성 컨텍스트의 변경사항을 DB에 Flush할 것인지를 결정
### Bulk 연산
- 다건의 update, delete 연산을 하나의 쿼리로 하는 것
- @Query에 벌크 연산 쿼리를 작성한 뒤 @Modifying 을 붙이지 않으면 InvalidDataAccessApiUsageException이 발생한다.


### Reference
---
https://frogand.tistory.com/174