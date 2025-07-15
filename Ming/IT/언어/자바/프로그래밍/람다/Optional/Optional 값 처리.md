> 값이 존재할 때와 존재하지 않을 때 처리하기 위한 메서드
## Optional 값 처리 메서드
- `ifPresent(Consumer<? super T> action)`
	- 값이 존재하면 action 실행
	- 값이 없으면 아무것도 안 함
- `ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)`
	- 값이 존재하면 action 실행
	- 값이 없으면 emptyAction 실행
- `map(Function<? super T, ? extends U> mapper)`
	- 값이 있으면 `mapper` 를 적용한 결과`(Optional<U>)` 반환
	- 값이 없으면 `Optional.empty()` 반환
- `flatMap(Function<? super T, ? extends Optional<? extends U>> mapper)`
	- map과 유사하지만, `Optional` 을 반환할 때 중첩되지 않고 평탄화(flat)해서 반
- `filter(Predicate<? super T> predicate)`
	- 값이 있고 조건을 만족하면 그대로 반환,
	- 조건 불만족이거나 비어있으면 `Optional.empty()` 반환
- `stream()`
	- 값이 있으면 단일 요소를 담은 `Stream<T>` 반환
	- 값이 없으면 빈 스트림 반환

---
## Optional 값 처리 예시
```java
Optional<String> optValue = Optional.of("Hello");  
Optional<String> optEmpty = Optional.empty();  
  
// 값이 존재하면 Consumer 실행, 없으면 아무 일도 하지 않음  
System.out.println("=== 1. ifPresent() ===");  
optValue.ifPresent(v -> System.out.println("optValue 값: " + v));  
optEmpty.ifPresent(v -> System.out.println("optEmpty 값: " + v));  
  
// 값이 있으면 Consumer 실행, 없으면 Runnable 실행  
System.out.println("=== 2. ifPresentOfElse() ===");  
optValue.ifPresentOrElse(v -> System.out.println("optValue 값: " + v),  
	() -> System.out.println("optValue는 비어있음"));  
optEmpty.ifPresentOrElse(v -> System.out.println("optEmpty 값: " + v),  
	() -> System.out.println("optEmpty는 비어있음"));  
  
// 값이 있으면 Function 적용 후 Optional로 반환, 없으면 Optional.empty()  
System.out.println("=== 3. map() ===");  
Optional<Integer> lengthOpt1 = optValue.map(String::length);  
System.out.println("optValue.map(String::length) = " + lengthOpt1);  
Optional<Integer> lengthOpt2 = optEmpty.map(String::length);  
System.out.println("optEmpty.map(String::length) = " + lengthOpt2);  
  
// map()과 유사하나, 이미 Optional을 반환하는 경우 중첩을 제거  
System.out.println("=== 4. flatMap() ===");  
// Optional[Hello] -> Optional[Optional[HELLO]]  
System.out.println("[map]");  
Optional<Optional<String>> nesteOpt = optValue.map(s -> Optional.of(s));  
System.out.println("optValue = " + optValue);  
System.out.println("nestedOpt = " + nesteOpt);  
  
// Optional[Hello] -> Optional[HELLO]  
System.out.println("[flatMap]");  
Optional<String> flaatenedOpt = optValue.flatMap(s -> Optional.of(s));  
System.out.println("optValue = " + optValue);  
System.out.println("flattenedOpt = " + flaatenedOpt);  

// 값이 있고 조건을 만족하면 그 값을 그대로, 불만족시 Optional.empty()  
System.out.println("=== 5. filter() ===");  
Optional<String> filtered1 = optValue.filter(s -> s.startsWith("H"));  
Optional<String> filtered2 = optValue.filter(s -> s.startsWith("X"));  
System.out.println("filter(H) = " + filtered1); // Optiona[Hello]  
System.out.println("filter(X) = " + filtered2); // Optional.empty  
  
System.out.println("=== 6. stream() ===");  
// 값이 있으면 단일 요소 스트리므 없으면 빈 스트림  
optValue.stream()  
	.forEach(s -> System.out.println("optValue.stream() -> " + s));  
// 값이 없음으로 실행 안 됨.  
optEmpty.stream()  
	.forEach(s -> System.out.println("optEmpty.stream() -> " + s));
```

```console
=== 1. ifPresent() ===
optValue 값: Hello

=== 2. ifPresentOfElse() ===
optValue 값: Hello
optEmpty는 비어있음

=== 3. map() ===
optValue.map(String::length) = Optional[5]
optEmpty.map(String::length) = Optional.empty

=== 4. flatMap() ===
[map]
optValue = Optional[Hello]
nestedOpt = Optional[Optional[Hello]]
[flatMap]
optValue = Optional[Hello]
flattenedOpt = Optional[Hello]

=== 5. filter() ===
filter(H) = Optional[Hello]
filter(X) = Optional.empty

=== 6. stream() ===
optValue.stream() -> Hello
```