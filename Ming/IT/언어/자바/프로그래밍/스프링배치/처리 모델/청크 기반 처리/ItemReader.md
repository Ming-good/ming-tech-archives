```java
public interface ItemReader<T> { 
	T read() throws Exception, 
					UnexpectedInputException, 
					ParseException, 
					NonTransientResourceException; 
}
```
- `read()`  
	- 아이템을 하나씩 반환한다. 
	- 데이터 소스에서 데이터를 순차적으로 일거오며 읽을 데이터가 더이상 없으면 null을 반환하며 스텝은 종료된다.
- 다양한 구현체 제공
	- Spring Batch는 파일, 데이터베이스, 메시지 큐 등 다양한 데이터 소스에 대한 표준 구현체를 제공한다
	- 예를들어, FlatFileItemReader는 CSV나 텍스트 파일에서 데이터를 읽어오고, JdbcCursorItemReader는 관계형 데이터베이스로부터 데이터를 읽어 온다.