![[Pasted image 20250810172334.png]]
- Publisher가 끊임없이 emit하는 무수히 많은 데이터를 적절하게 제어하여 데이터 처리에 과부하가 걸리지 않도록 제어하는 것

## `BaseSubscriber()` 사용 예시
```java
Flux.range(1, 5)  
	.doOnRequest(data -> Log.info("# doOnRequest: {}", data))  
	.subscribe(new BaseSubscriber<Integer>() {  
		@Override  
		protected void hookOnSubscribe(Subscription subscription) {  
			request(1);  
		}  
  
		@Override  
		protected void hookOnNext(Integer value) {  
			try {  
				Thread.sleep(2000L);  
			} catch (InterruptedException e) {  
				throw new RuntimeException(e);  
			}  
	  
			Log.info("# hookOnNext: {}", value);  
			request(1);  
		}  
});
```
1. `subscribe(new BaseSubscriber)`: 백프레션 선언
2. `hookOnSubscribe(Subscription subscription)`
	- `onSubscribe()`메서드 대신
	- 구독 시점에 `request()`호출을 통해 최초 데이터 요청 개수 제어
3. `hookOnNext(Integer value)`
	- `onNext()` 메서드 대신
	- Publisher가 emit한 데이터를 전달받아 처리한 후에 Publisher에게 또다시 요청하는 역할
	- `request()`로 데이터 요청 개수 제어
4. `doOnRequest()`: Subscriber가 요청한 데어터의 요청 개수 출력
