```java
private static void shutdownAndAwaitTermination(ExecutorService es) {  
		es.shutdown(); // non-blocking, 새로울 작업을 받지 않는다. 처리중이거나, 큐에 이미 대기중인 작업은 처리한다. 이후 풀의 스레드를 처리한다.  
		try {  
		// 이미 대기중인 작업들을 모두 완료할 때 까지 10초 대기  
		if (!es.awaitTermination(10, TimeUnit.SECONDS)) {  
			// 정상 종료가 너무 오래 걸리면...  
			log("서비스 정상 종료 실패 -> 강제 종료 시도");  
			es.shutdownNow();  
		  
			// 작업이 취소될 때 까지 대기된다.  
			if (!es.awaitTermination(10, TimeUnit.SECONDS)) {  
				log("서비스가 종료되지 않았습니다.");  
			}  
		}  
	} catch (InterruptedException e) {  
		// awaitTermination()으로 대기중인 현재 스레드가 종료될 수 있다.  
		es.shutdownNow();  
	}  
}
```
##### 서비스 종료 실패 (shutdownNow호출후 10초 기다리는 이유)
- shutdownNow()가 작업 중인 스레드에 인터럽트를 호출하는 것은 맞다. 인터럽트 이후 자원 정리하는 간단한 작업을 수행 할 수 도 있어서 기다려주는 것.

##### 인터럽트 받을 수 없는 코드
```java
while(true) { }
```

