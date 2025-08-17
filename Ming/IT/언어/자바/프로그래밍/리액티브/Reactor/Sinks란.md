> 여러 스레드에서 각각의 전혀 다른 작업들을 처리한 후, 처리 결과를 반환하는 상황에 사용할 수 있는 방법으로 멀티스레드 환경에스 스레드 세이프하다.(모아서 -> 결과만 처리)
- Sinks는 Publisher와 Subscriber의 기능을 모두 지닌 Processor의 향상된 기능을 제공한다.
## Sinks 종류와 특징
#### `Sinks.One()`
```java
public static <T> Sinks.One<T> one() {  
	return SinksSpecs.DEFAULT_SINKS.one();  
}
```
- 한 건의 데이터를 프로그래밍방식으로 emit
- Mono 방식으로 Subscriber가 데이터를 소비할 수 있게 해주는 Sinks이다.

```java
One<Object> sinkOne = Sinks.one();  
Mono<Object> mono = sinkOne.asMono();  
  
sinkOne.emitValue("Hello Reactor", EmitFailureHandler.FAIL_FAST);  
  
mono.subscribe(data -> log.info("# Subscriber1: " + data));  
mono.subscribe(data -> log.info("# Subscriber2: " + data));
```

```console
16:38:55.370 [main] INFO SinksOneMain1 - # Subscriber1: Hello Reactor
16:38:55.370 [main] INFO SinksOneMain1 - # Subscriber2: Hello Reactor
```
- `EmitFailureHandler.FAIL_FAST`: 에러가 발생했을 때 재시도를 하지 않고 즉시 실패 처리 한다.

#### `Sinks.Many()`
```java
public static ManySpec many() {  
	return SinksSpecs.DEFAULT_SINKS.many();  
}

public interface ManySpec {  
UnicastSpec unicast();  
MulticastSpec multicast();  
MulticastReplaySpec replay();  
}
```
- `unicast()`
	- Unicast는 하나의 특정 시스템만 정보를 전달받는 One to One 방식
- `multicast()`
	- Multicast는 일부 시스템들만 정보를 전달받는 One to Many방식
	- Hot Publisher로 동작하며 Warm up의 특징을 가지고 있다.

1. `unicast()` 예시
```java
Many<Object> unicastSink = Sinks.many().unicast().onBackpressureBuffer();  
Flux<Object> fluxView = unicastSink.asFlux();  
  
unicastSink.emitNext(1, EmitFailureHandler.FAIL_FAST);  
unicastSink.emitNext(2, EmitFailureHandler.FAIL_FAST);  
  
fluxView.subscribe(data -> log.info("# Subscriber1: {}", data));  
unicastSink.emitNext(3, EmitFailureHandler.FAIL_FAST);  
fluxView.subscribe(data -> log.info("# Subscriber2: {}", data));
```

```console
16:51:44.731 [main] INFO - # Subscriber1: 1
16:51:44.731 [main] INFO - # Subscriber1: 2
16:51:44.731 [main] INFO - # Subscriber1: 3
16:51:44.733 [main] ERROR reactor.core.publisher.Operators - Operator called default onErrorDropped
```
> `UnicastSpec`은 단 하나의 Subscriber에게만 데이터를 emit하는 것이기 때문에 두번 째 Subscriber에게 `emit`시 에러가 발생한다.


2. `multicast()` 예시
```java
Many<Object> multicastSink = Sinks.many().multicast().onBackpressureBuffer();  
Flux<Object> fluxView = multicastSink.asFlux();  
  
multicastSink.emitNext(1, EmitFailureHandler.FAIL_FAST);  
multicastSink.emitNext(2, EmitFailureHandler.FAIL_FAST);  
  
fluxView.subscribe(data -> log.info("# Subscriber1: {}", data));  
fluxView.subscribe(data -> log.info("# Subscriber2: {}", data));  
  
multicastSink.emitNext(3, EmitFailureHandler.FAIL_FAST);
```

```console
17:33:27.859 [main] INFO - # Subscriber1: 1
17:33:27.860 [main] INFO - # Subscriber1: 2
17:33:27.860 [main] INFO - # Subscriber1: 3
17:33:27.860 [main] INFO - # Subscriber2: 3
```
- Subscriber2가 1과 2의 데이터를 못 받은 것은 `multicast()`는 Warm up의 특징이 있기 때문

3. `replay()` 예시
```java
Many<Object> replaySink = Sinks.many().replay().limit(2);  
Flux<Object> fluxView = replaySink.asFlux();  
  
replaySink.emitNext(1, EmitFailureHandler.FAIL_FAST);  
replaySink.emitNext(2, EmitFailureHandler.FAIL_FAST);  
replaySink.emitNext(3, EmitFailureHandler.FAIL_FAST);  
  
fluxView.subscribe(data -> log.info("# Subscriber1: {}", data));  
  
replaySink.emitNext(4, EmitFailureHandler.FAIL_FAST);  
  
fluxView.subscribe(data -> log.info("# subscriber2: {}", data));
```

```console
18:18:21.525 [main] INFO - # Subscriber1: 2
18:18:21.525 [main] INFO - # Subscriber1: 3
18:18:21.525 [main] INFO - # Subscriber1: 4
18:18:21.525 [main] INFO - # subscriber2: 3
18:18:21.525 [main] INFO - # subscriber2: 4
```
- emit된 데이터를 다시 replay해서 구독 전에 이미 emit된 데이터라도 Subscriber가 전달 받을 수 있게 하는 다양한 메서드들이 정의되어 있다.
- 대표적인 메서드
	- `all()`: MP3플레이어의 replay버튼과 같은 기능을 제공한다
	- `limit()`: emit된데이터 중에서 파라미터로 입력한 개수만큼 나중에 emit된 데이터부터 Subscriber에게 전달하는 기능이다.