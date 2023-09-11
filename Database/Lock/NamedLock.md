## NamedLock
- 테이블이나 레코드, 데이터베이스 객체가 아닌 사용자가 지정한 문자열에 대해 락을 획득하고 반납하는 잠금
- Mysql을 사용해 분산락을 구현할 수 있다.
- 전파레벨을 Requires_new를 활용하여 부모 트랜잭션과 분리해야 한다.
```
public class ItemService {
		// 부모 트랜잭션과 분리
		@Transactional(propagation = Propagation.REQUIRES_NEW)
		public void buyItem(Long id, Long quantity) {
		    Item item = itemRepository.findById(id)
		            .orElseThrow(() -> new ItemNotExistException("아이템이 존재하지 않습니다"));
		    item.decreaseStock(quantity);
		}
}
```
왜냐하면 트랜잭션을 나갈 때 commit이 되는 데, 이때 락이 해제되기 때문이다.
만약에 트랜잭션을 부모와 함께하게 된다면 락을 해제한 뒤 커밋이 되기 때문에 
### Reference
---
https://sudal.site/namedLock/
https://velog.io/@hyojhand/named-lock-distributed-lock-with-redis