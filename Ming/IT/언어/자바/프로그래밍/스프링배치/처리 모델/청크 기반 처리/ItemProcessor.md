>**ItemProcessor**는 데이터를 원하는 형태로 깎아내는 가공 담당자다. ItemReader가 넘긴 원재료를 받아다가, 필요한 모양으로 다듬는 작업을 한다.


```java
public interface ItemProcessor<I, O> { 
	O process(I item) throws Exception; 
}
```
- **데이터 가공** 
	- 입력 데이터`I`를 `O`로 변환한다.
- 필터링 
	- null을 반환하면 해당 데이터는 처리 흐름에서 제외된다. 
	- 즉, `ItemWriter`로 전달되지 않는다.
- **데이터 검증**
	- 입력 데이터의 유효성 검증 할 수 있다.
	- 예외를 발생시킴으로 잡을 중단할 수 있다
- 생략가능