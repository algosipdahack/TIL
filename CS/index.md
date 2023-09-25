### index 성능
- full scan보다 더 빨리 찾을 수 있다.
- B-tree based index -> O(logn)
- 조건을 만족하는 튜플들을 빠르게 조회하기 위함

- primary key에는 자동 index가 걸린다.


### show index from player;
= 해당 테이블에 걸린 인덱스를 보여줌