| 종류        | 설명                                                                                |
| --------- | --------------------------------------------------------------------------------- |
| IGNORE 전략 | Backpressure를 적용하지 않는다.                                                           |
| ERROR 전략  | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, Exception을 발생시키는 전략                            |
| DROP 전략   | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 먼저 emit된 데이터부터 Drop시키는 전략          |
| LATEST 전략 | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 가장 최근에(나중에) emit된 데이터부터 버퍼에 채우는 전략 |
| BUFFER 전략 | Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 안에 있는 데이터부터 Drop시키는 전략                      |

#### ERROR 전략
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
