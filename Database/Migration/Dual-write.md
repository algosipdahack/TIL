## Dual-write란?
- 데이터 마이그레이션 수행 동안 클라이언트는 source/target DB모두에게 데이터를 읽고 쓴다.

### 방법
1. new DB 스키마 생성
2. new DB에도 데이터를 insert할 수 있는 DAO 생성
3. new 에도 write시작
4. new를 primary DB로 변경
5. old DB 쓰기 중단
6. new에 없는 데이터 마이그레이션
7. old의 DAO 제거

### 단점
- 클라이언트의 코드가 변경되어야 한다.
- 배포가 두번이나 이루어져야 한다.