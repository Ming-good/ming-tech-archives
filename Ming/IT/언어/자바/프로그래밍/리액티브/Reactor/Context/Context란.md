> 어떠한 상황을 처리하거나 해결하기 위해 필요한 정보를 제공하는 어떤 것

## Reactor에 Context 특징
- 리액터에 Context는 ThreadLocal과 달리 다소 유사한 면이 있지만, 각각 실행 스레드와 매핑되는 ThreadLocal과 달리 실행 스레드와 매핑되는 것이 아니라 Subscriber와 매핑된다.
- Context에서 데이터를 읽어오는 방식
	- 원본 데이터 소스레벨에서 읽는 방식
	- 연산자 체인 중간에서 읽는 방식
- 연산자 체인에 전파되는 키와 값 형태의 저장소이다.

## Context 사용 예시
```java
Mono.deferContextual(ctx -> Mono.just("Hello" + " " + ctx.get("firstName"))  
	.doOnNext(data -> log.info("# just doOnNext: {}", data))  
	)
	.subscribeOn(Schedulers.boundedElastic())  
	.publishOn(Schedulers.parallel())  
	.transformDeferredContextual(  
		(mono, ctx) -> mono.map(data -> data + " " + ctx.get("lastName"))  
	)  
	.contextWrite(ctx -> ctx.put("lastName", "Jobs"))  
	.contextWrite(ctx -> ctx.put("firstName", "Steav"))  
	.subscribe(data -> log.info("# onNext: {}", data));  
Thread.sleep(1000L);
```

```console
18:58:04.824 [boundedElastic-1] INFO - # just doOnNext: Hello Steav
18:58:04.831 [parallel-1] INFO - # onNext: Hello Steav Jobs
```
> **첫 번째 읽어오는 작업**과 **두 번째 읽어오는 작업**은 스레드가 분리되어 있음에도 **동일한 Context**의 저장된 데이터를 읽을 수 있다.
- `deferContextual(ContextView)`: `defer()`와 동일한 원리로 동작한다. Context에 저장된 데이터와 원본 데이터 소스의 처리를 지연시키는 역할을 한다.
- `transformDefferedContextual()`: 파이프라인 중간에서 Context 데이터를 읽을 수 있다.
- `Context`
	- ContextView 타입 객체: Context에 저장된 데이터를 읽을 때 사용
	- Context 타입 객체: 데이터를 쓰는 작업을 할 수 있다.
