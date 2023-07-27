### 레디스의 특징
- 비동기 기반
  - 싱글스레드와 이벤트 루프로 동작
- 추가 연산 시 O(1) 시간 소요
- TCP 기반의 네트워크 모델을 따름
  - 네트워크 I/O에서 병목이 생길 수 있음
  - 요청을 보내고 응답을 받을 때까지는 blocking상태
    - 5번의 insert 쿼리를 보낼 시 redis에서는 클라이언트 요청 -> 서버의 응답을 5번 반복 -> RTT(Round Trip Time)이 길어짐
    - 따라서 처음에 한번 레디스에 연결한 뒤 수십만개의 원소를 추가하고 응답을 한번 받는 방식으로 로직을 변경 시 성능향상
      - HTTP Pipelining과 비슷한 개념
      - Mysql에서는 bulk insert를 지원. redis는 지원X -> 대신 redis pipeline 지원

### Redis Pipeline
- 수많은 요청을 보내기 위해 Pipeline을 형성한 후 모든 request를 Pipeline에 실어서 한번에 보냄
- 한번에 여러개의 명령어를 전송 후 비동기적으로 그에 대한 Response를 받아옴
- executePipelined 메소드를 통해 Pipeline을 시작가능 -> 해당 connection에 직접 명령어를 넣음
```
public TextMemoState saveToRedis(TextMemoState textMemoState) {
	
    // 파이프라인 start
    redisTemplate.executePipelined((RedisCallback<Object>) connection -> {

        Map<String, Object> map = objectMapper.convertValue(textMemoState, Map.class);

        // Request 1. Redis Set에 멤버 add
        connection.sAdd(keySerializer.serialize(defaultStateKey), valueSerializer.serialize(defaultStateKey + "_history:" + textMemoState.getId()));

        // 각각의 객체들 redis에 hash 타입으로 set
        for (String key : map.keySet()) {

            // Request 2. hash set 
            connection.hashCommands().hSet(keySerializer.serialize(defaultStateKey + "_latest"),
                    valueSerializer.serialize(key), valueSerializer.serialize(map.get(key)));

            // Request 3. hash set 
            connection.hashCommands().hSet(keySerializer.serialize(defaultStateKey + "_history:" + textMemoState.getId()),
                    valueSerializer.serialize(key), valueSerializer.serialize(map.get(key)));
        }

        return null;
    });

    return textMemoState;
}

```

### Reference
---
https://tjdrnr05571.tistory.com/7
https://velog.io/@jsb100800/redis-pipelining