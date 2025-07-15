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

