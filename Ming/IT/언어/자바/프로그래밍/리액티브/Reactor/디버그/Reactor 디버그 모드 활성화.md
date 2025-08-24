## `Hooks.onOperatorDebug()`
- 한계: 디버그 모드 활성화시 비용이 많이 드는 동작 과정을 거친다.
	- 애플리케이션 내에 있는 모든 연산자의 스택트레이스를 캡처한다.
	- 에러가 발생하면 캡처한 정보를 기반으로 에러가 발생한 [[Assembly]]의 스택트레이스를 원본 스택트레이승 중간에 넣는다.

## 프로덕션 환경에서의 디버깅 설정
![[Pasted image 20250824192822.png]]

## `checkpoint()`를 사용한 디버깅
```java
Flux.just(2, 4, 6, 8)  
	.zipWith(Flux.just(1, 2, 3, 0), (x, y) -> x / y)  
	.map(num -> num + 2)  
	.checkpoint()  
	.subscribe(  
		data -> log.info("# onNext: {}", data),  
		err -> log.error("# onErr: {}", err)  
	);
```
