## 스레드 관리 연산자
#### `subscribeOn()`
- 구독이 발생한 직후 실행될 스레드를 지정하는 연산자이다.
- `doOnSubscribe()`: 구독이 발생한 시점에 추가적인 어떤 처리가 필요할 경우 사용됨.
```java
Flux.fromArray(new Integer[]{1, 3, 5, 7})  
	.subscribeOn(Schedulers.boundedElastic())  
	.doOnNext(data -> log.info("# doOnNext: {}", data))  
	.doOnSubscribe(subscription -> log.info("# doOnSubscribe"+ subscription))  
	.subscribe(data -> log.info("# onNext: {}", data));  
	  
Thread.sleep(500L);
```

```console
22:42:15.375 [main] INFO - # doOnSubscribe reactor.core.publisher.FluxPeek$PeekSubscriber@30ee2816
22:42:15.377 [boundedElastic-1] INFO - # doOnNext: 1
22:42:15.377 [boundedElastic-1] INFO - # onNext: 1
22:42:15.377 [boundedElastic-1] INFO - # doOnNext: 3
22:42:15.377 [boundedElastic-1] INFO - # onNext: 3
22:42:15.377 [boundedElastic-1] INFO - # doOnNext: 5
22:42:15.377 [boundedElastic-1] INFO - # onNext: 5
22:42:15.377 [boundedElastic-1] INFO - # doOnNext: 7
22:42:15.377 [boundedElastic-1] INFO - # onNext: 7
```
#### `publishOn()`
- Downstream으로 Signal을 전송할 때 실행되는 스레드를 제어하는 역할을 하는 연산자이다.
```java
Flux.fromArray(new Integer[]{1, 3, 5, 7})  
	.doOnNext(data -> log.info("# doOnNext: {}", data))  
	.doOnSubscribe(subscription -> log.info("# doOnSubscribe"))  
	.publishOn(Schedulers.parallel())  
	.subscribe(data -> log.info("# onNext: {}", data));  
	  
Thread.sleep(500L);
```

```console
22:51:11.644 [main] INFO - # doOnSubscribe
22:51:11.646 [main] INFO - # doOnNext: 1
22:51:11.646 [main] INFO - # doOnNext: 3
22:51:11.646 [main] INFO - # doOnNext: 5
22:51:11.646 [main] INFO - # doOnNext: 7
22:51:11.647 [parallel-1] INFO - # onNext: 1
22:51:11.647 [parallel-1] INFO - # onNext: 3
22:51:11.647 [parallel-1] INFO - # onNext: 5
22:51:11.647 [parallel-1] INFO - # onNext: 7
```
#### `parallel().runOn()`
- 물리적인 스레드 연산자
- 라운드 로빈 방식으로 CPU코어(물리적인 스레드) 개수만큼의 스레드를 병렬로 실행
- 병렬 처리 사용을 위해서는 `parallel()`연산자 추가후 `runOn()`연산자로 병렬 작업을 진행한다.
> Reactor에서는 라운드 로빈 방식으로 CPU의 논리적인 코어 수에 맞게 데이터를 그룹화한 것을 'rail'이라고 한다.
```java
Flux.fromArray(new Integer[]{1, 3, 5, 7, 9, 11, 13, 15, 17, 19})  
	.doOnSubscribe(subscription -> log.info("### publishOn() 사용시"))  
	.publishOn(Schedulers.parallel())  
	.doOnNext(data -> log.info("# onNext: {}", data))  
	.doOnTerminate(() -> log.info("############\n"))  
	.subscribe();  
  
  
Flux.fromArray(new Integer[]{1, 3, 5, 7, 9, 11, 13, 15, 17, 19})  
	.doOnSubscribe(subscription -> log.info("### parallel() 사용시"))  
	.parallel(4)  
	.runOn(Schedulers.parallel())  
	.subscribe(data -> log.info("# onNext: {}", data));  
  
Thread.sleep(200L);
```

```console
23:08:51.845 [main] INFO - ### publishOn() 사용시
23:08:51.848 [parallel-1] INFO - # onNext: 1
23:08:51.848 [parallel-1] INFO - # onNext: 3
23:08:51.848 [parallel-1] INFO - # onNext: 5
23:08:51.848 [parallel-1] INFO - # onNext: 7
23:08:51.848 [parallel-1] INFO - # onNext: 9
23:08:51.848 [parallel-1] INFO - # onNext: 11
23:08:51.848 [parallel-1] INFO - # onNext: 13
23:08:51.848 [parallel-1] INFO - # onNext: 15
23:08:51.848 [parallel-1] INFO - # onNext: 17
23:08:51.848 [parallel-1] INFO - # onNext: 19
23:08:51.848 [parallel-1] INFO - ############

23:08:51.850 [main] INFO - ### parallel() 사용시
23:08:51.851 [parallel-3] INFO - # onNext: 3
23:08:51.851 [parallel-5] INFO - # onNext: 7
23:08:51.851 [parallel-3] INFO - # onNext: 11
23:08:51.851 [parallel-2] INFO - # onNext: 1
23:08:51.851 [parallel-3] INFO - # onNext: 19
23:08:51.851 [parallel-4] INFO - # onNext: 5
23:08:51.851 [parallel-5] INFO - # onNext: 15
23:08:51.851 [parallel-2] INFO - # onNext: 9
23:08:51.851 [parallel-4] INFO - # onNext: 13
23:08:51.851 [parallel-2] INFO - # onNext: 17
```

