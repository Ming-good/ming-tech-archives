> 어떠한 상황을 처리하거나 해결하기 위해 필요한 정보를 제공하는 어떤 것

## Reactor에 Context 특징
1. Context에서 데이터를 읽어오는  2가지 방식
	- 원본 데이터 소스레벨에서 읽는 방식
	- 연산자 체인 중간에서 읽는 방식
2. 연산자 체인에 전파되는 키와 값 형태의 저장소이다.
3. Context는 구독이 발생할 떄마다 하나의 Context가 해당 구독에 연결된다.
	- 리액터에 Context는 ThreadLocal과 달리 다소 유사한 면이 있지만, 각각 실행 스레드와 매핑되는 ThreadLocal과 달리 실행 스레드와 매핑되는 것이 아니라 Subscriber와 매핑된다.
4. Context는 연산자 체인의 아래에서 위로 전파된다.
	- 동일한 키에 대한 중복값은 체인의 상단의 `contextWrite()`이 저장한 값으로 덮어쓴다.
5. 내부 Context는 외부에 Context를 읽을 수 있지만 외부에서 내부를 읽을 수 없다.
	1. 외부에 Context는 내부의 Context를 읽으려하면 `NoSuchElementException`이 발생한다.
6. Context는 인증 정보와 같은 직교성(독립성)을 가지는 정보를 전송하는데 적합하다.

## Context 사용 예시
#### 1. 스레드 간에 Context 공유 예시
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

#### 2. `Context`의 전파 방식(아래 -> 위)
```java
final String key1 = "company";  
final String key2 = "name";  
  
Mono.deferContextual(ctx -> Mono.just(ctx.get(key1)))  
	.publishOn(Schedulers.parallel())  
	.contextWrite(context -> context.put(key2, "Bill"))  
	.transformDeferredContextual(  
		(mono, ctx) -> mono.map(
			data -> data + ", " + ctx.getOrDefault(key2, "Steave")
		)  
	)  
	.contextWrite(context -> context.put(key1, "Apple"))  
	.subscribe(data -> log.info("# onNext:{}", data));
```

```console
17:19:05.487 [parallel-1] INFO - # onNext:Apple, Steave
```
> 'Bill'이 아닌, "Steave"가 출력되었다. 그 이유는 Context의 경우, 연산제 체인상의 **아래에서 위로 전파되는 특징** 때문이다.

#### 3. 내부 Context와 외부 Context
```java
String key1 = "company";  
Mono.just("Steave")  
// .transformDeferredContextual((mono, ctx) -> ctx.get("role"))  
	.flatMap(name ->  
		Mono.deferContextual(innerCtx ->  
			Mono.just(innerCtx.get(key1) + ", " + name)  
				.transformDeferredContextual((mono, ctx) ->  
					mono.map(data -> data + ", " + ctx.get("role"))  
				)  
				.contextWrite(context -> context.put("role", "CEO"))  
				)  
			)  
	.publishOn(Schedulers.parallel())  
	.contextWrite(context -> context.put(key1, "Apple"))  
	.subscribe(data -> log.info("# onNext: {}", data));  
  
```

```console
17:43:43.906 [parallel-1] INFO - # onNext: Apple, Steave, CEO
```
> 내부 Context는 "Apple"를 읽어올 수 있지만 외부 Context는 "role"를 읽을 수 없다.

