| 종류        | 설명                                                                                |
| --------- | --------------------------------------------------------------------------------- |
| IGNORE 전략 | Backpressure를 적용하지 않는다.                                                           |
| ERROR 전략  | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, Exception을 발생시키는 전략                            |
| DROP 전략   | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 먼저 emit된 데이터부터 Drop시키는 전략          |
| LATEST 전략 | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 가장 최근에(나중에) emit된 데이터부터 버퍼에 채우는 전략 |
| BUFFER 전략 | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 안에 있는 데이터부터 Drop시키는 전략                      |

## ERROR 전략 - `onBackpressureError()`
```java
Flux  
	.interval(Duration.ofMillis(1L))  
	.onBackpressureError()  
	.doOnNext(data -> Log.info("# doOnNet: {}", data))  
	.publishOn(Schedulers.parallel())  
	.subscribe(data -> {  
		try {  
			Thread.sleep(5L);  
		} catch (InterruptedException e) {  
			Log.info("# onNext: {}", data);  
		}  
	},  
		error -> Log.info("# onError")
	);  
Thread.sleep(2000L);
```

```console
17:59:08.646 [parallel-1] - # onNext: 254
17:59:08.653 [parallel-1] - # onNext: 255
17:59:08.655 [parallel-1] - # onError: {}
reactor.core.Exceptions$OverflowException: The receiver is overrun by more signals than expected
```

## DROP 전략 - `onBackpressureDrop()`
![[Pasted image 20250810180204.png]]
```java
Flux  
	.interval(Duration.ofMillis(1L))  
	.onBackpressureDrop(drop -> Log.info("# dropped: {}", drop))  
	.publishOn(Schedulers.parallel())  
	.subscribe(data -> {  
		try {  
			Thread.sleep(5L);  
		} catch (InterruptedException e) {  
		}  
		Log.info("# onNext: {}", data);  
	},  
		error -> Log.info("# onError: {}", error)
	);  
Thread.sleep(2000L);
```

## LATEST 전략 - `onBackpressureLatest()`
![[Pasted image 20250810181059.png]]
> DROP전략과 차이점은 LATEST는 최신 데이터 하나만 남기고 모두 DROP한다는 것이고 DROP 전략은 오래된 데이터를 연속적으로 버리는 것
- 사용 예시: 주식시세, IoT센서에서 '현재값'만 필요할 때

# BUFFER 전략 - 
> BUFFER 전략은 BUFFER가 가득찼을 때 바깥쪽이 아닌 버퍼 안에 데이터를 폐기하는 전략이다.
#### DROP_LATEST전략
![[Pasted image 20250810182051.png]]
```java
Flux  
	.interval(Duration.ofMillis(300L))  
	.doOnNext(data -> Log.info("# emitted by original Flux: {}", data))  
	.onBackpressureBuffer(  
		2,  
		drop -> Log.info("*** Overflow & Dropped: {} ***", drop),  
		BufferOverflowStrategy.DROP_LATEST  
	)  
	.doOnNext(data -> Log.info("[ # emitted by Buffer: {} ]", data))  
	.publishOn(Schedulers.parallel(), false, 1)  
	.subscribe(data -> {  
			try {  
				Thread.sleep(1000L);  
			} catch (InterruptedException e) {  
			}  
			Log.info("# onNext: {}", data);  
		},  
		error -> Log.info("# onError: {}", error)  
	);  
Thread.sleep(3000L);
```

```console
18:24:31.891 [parallel-2] - # emitted by original Flux: 0
18:24:31.892 [parallel-2] - [ # emitted by Buffer: 0 ]
18:24:32.193 [parallel-2] - # emitted by original Flux: 1
18:24:32.490 [parallel-2] - # emitted by original Flux: 2
18:24:32.793 [parallel-2] - # emitted by original Flux: 3
18:24:32.795 [parallel-2] - *** Overflow & Dropped: 3 ***
```
> `publishOn()` 연산자의 세 번째 파라미터로 사용한 prefetch는 Scheduler가 생성하는 스레드 비동기 경계 시점에 미리 보관할 데이터의 개수를 의미하며, 데이터의 요청 개수에 영향을 미침

#### BUFFER DROP_OLDEST 전략
> 버퍼 안에 채워진데이터 중에서 가장 오래된 데이터를 Drop하는 전략
![[Pasted image 20250810183014.png]]
