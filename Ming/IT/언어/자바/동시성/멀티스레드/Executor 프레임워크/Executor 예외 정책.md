##### **AbortPolicy** 
```java
// 선언
new ThreadPoolExecutor(1, 1, 0, TimeUnit.SECONDS, new SynchronousQueue<>(), new 
ThreadPoolExecutor.AbortPolicy());
```

```java
public static class AbortPolicy implements RejectedExecutionHandler {
	public void rejectedExecution(Runnable r, ThreadPoolExecutor e) { throw new RejectedExecutionException("Task " + r.toString() +" rejected from " + e.toString());
	}
}
```
- 새로운 작업을 제출할 때 `RejectedExecutionException` 을 발생시킨다.
- 기본 정책으로 생략 해도 된다.
  
##### **DiscardPolicy**
```java
new ThreadPoolExecutor(1, 1, 0,TimeUnit.SECONDS,new SynchronousQueue<>(), new ThreadPoolExecutor.DiscardPolicy());
```

```java
public static class DiscardPolicy implements RejectedExecutionHandler {
	public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
	// empty
	}
}
```
- 새로운 작업을 조용히 버린다.

#####  **CallerRunsPolicy**
```java
new ThreadPoolExecutor(1, 1, 0,TimeUnit.SECONDS,new SynchronousQueue<>(), new ThreadPoolExecutor.CallerRunsPolicy());
```

```java
public static class CallerRunsPolicy implements RejectedExecutionHandler {
	public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
		if (!e.isShutdown()) {
			r.run();
		}
	}
}
```
- 새로운 작업을 제출한 스레드가 대신해서 직접 작업을 실행한다.
- 호출한 스레드가 직접 작업을 수행하게 한다. 이로 인해 새로운 작업을 제출하는 스레드의 속도가 느려질 수 있다.
- `ThreadPoolExecutor` 를 `shutdown()` 을 하면 이후에 요청하는 작업을 거절하는데, 이때도 같은 정책이 적용된다.
- 그런데 `CallerRunsPolicy` 정책은 `shutdown()` 이후에도 **작업을 수행**해버린다. 따라서 `shutdown()`조건을 체크해서 이 경우에는 작업을 수행하지 않도록 한다.

##### **사용자 정의**(`RejectedExecutionHandler` )
```java
new ThreadPoolExecutor(1, 1, 0,TimeUnit.SECONDS,new SynchronousQueue<>(), new MyRejectedExecutionHandler());



class MyRejectedExecutionHandler implements RejectedExecutionHandler {  
	static AtomicInteger count = new AtomicInteger(0);  
		@Override  
	public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {  
		int i = count.incrementAndGet();  
		log("[ERROR] reject count : " + i);  
	}  
}
```

- 개발자가 직접 정의한 거절 정책을 사용할 수 있다.