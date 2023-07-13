### Delete 방식
- Hard Delete
  - 실제로 값을 삭제하는 방식
- Soft Delete
  - 데이터를 실제로 삭제하지 않고 삭제 flag를 변경하는 방식
  - Bulk연산 시 Soft Delete를 사용할 때에 여러번의 쿼링이 진행
  - 해결방식
    - @SQLDelete, deleteAllInBatch

### @SQLDelete
- 삭제 방식을 @SQLDelete에서 미리 작성해둘 시 delete가 호출될 때에 방식을 변경 가능
```
@SQLDelete(sql = "UPDATE board SET is_deleted = true WHERE id = ?")
```
- Spring Data JPA에서 delete 메소드를 호출할 때에 삭제 대신 미리 정의한 쿼리를 호출시켜준다.
```
public void deleteBoard(Long BoardId) {
    boardRepository.deleteById(boardId)
}
```
- 이후 softDelete 가능

- 하지만 이후 게시글을 보여줄 때 where절로 delete되지 않은 애들만 보여줄 수 있도록 해야 하기에 귀찮을 수 있다.
- 이러한 문제점을 @Where로 해결 가능
```
@Where(clause = "is_deleted = true")
```
이렇게 하면 엔티티 자체의 검색에 관한 필터링을 걸어둘 수 있다.