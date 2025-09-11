> **ItemWriter**는 ItemProcessor가 만든 결과물을 받아, 원하는 방식으로 최종 저장/출력한다. - 데이터를 DB에 `INSERT`, 파일에 `WRITE`, 메시지 큐에

```java
public interface ItemWriter<T> { 
	void write(Chunk<? extends T> chunk) throws Exception; 
}
```
- `write()`
	- Chunk 단위로 묶어서 데이터를 처리한다
- 다양한 구현체 제공
	- Spring Batch는 파일, 데이터베이스, 외부 시스템 전송 등에 사용할 수 있는 다양한 구현체를 제공한다
	- 예를 들어, FlatFileItemWriter는 파일에 데이터를 기록하고, JdbcBatchItemWriter는 데이터베이스에 데이터를 저장한다.
