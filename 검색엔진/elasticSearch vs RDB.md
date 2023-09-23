### ElasticSearch vs RDB like 쿼리
- 기존의 like 쿼리에서 ElasticSearch의 사전을 통해 검색 기능 향상 이유
    
    확장성을 고려함
    
    우리 서비스의 경우 아티클이 가장 사용자 유입에 결정적인 영향을 미치는데, 아티클 내용을 검색 시에 성능이 떨어지는 문제점이 발생. RDB는 대용량 데이터를 검색할때 성능이 떨어지기 때문.
- 복잡한 텍스트 검색 | 유사성 기반 검색 -> 부적합
- elasticSearch는 역인덱스 활용


### 역인덱스란
- 특정 단어를 분석하여 해당 단어가 어느 위치에 있는지를 기록
-  
### Reference
---
https://goodbyeanma.tistory.com/160