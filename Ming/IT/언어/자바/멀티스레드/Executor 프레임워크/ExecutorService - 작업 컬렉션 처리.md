##### invokeAll()
```java
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException
```
- 모든 Callable 작업을 제출하고, 모든 작업이 완료될 때까지 기다린다.

```java
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException
``` 
- 지정된 시간 내에 모든 `Callable` 작업을 제출하고 완료될 때까지 기다린다.
##### invokeAny()
```java
<T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException
```
-  하나의 `Callable` 작업이 완료될 때까지 기다리고, 가장 먼저 완료된 작업의 결과를 반환한다. 
-  완료되지 않은 나머지 작업은 취소한다.
```java
<T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout,TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException
```
-  지정된 시간 내에 하나의 `Callable` 작업이 완료될 때까지 기다리고, 가장 먼저 완료된 작업의 결과반환한다.
- 완료되지 않은 나머지 작업은 취소한다. (Interrupt 처리)

--- 
### ExecutorService의 종료 메서드
##### 서비스 종료
- `void shutdown()`
![[Pasted image 20250518162230.png]]
	- 논 블로킹 메서드
	- 새로운 작업을 받지 않고, 이미 제출된 작업을 모두 완료한 후에 종료한다.
- `List<Runnable> shutdownNow()`
![[Pasted image 20250518162148.png]]
	- 논 블로킹 메서드
	- 실행 중인 작업을 중단하고, **대기 중인 작업을 반환**하며 즉시 종료한다.
	- 실행 중인 작업을 중단하기 위해 인터럽트를 발생시킨다.

##### 서비스 상태 확인
- `boolean isShutdown()`
	- 서비스 종료되었는지 확인
- `boolean isTerminated()`
	- shutdown(), shutdownNow()를 호출 후, 모든 작업이 완료되었는지 확인한다.

##### 작업 완료 대기
- boolean awaitTermination(long timeout, TileUnit unit) throws InterruptedException
	-  서비스 종료시 모든 작업이 완료될 때까지 대기한다. 
	-  블로킹 메서드

##### close()
> close()는 자바 19부터 지원하는 서비스 종료 메서드이다. 이 메서드는 shutdown()과 같다고 생각하면 된다.
- shutdown()을 호출하고 하루를 기다려도 작업이 완료되지 않으면 shutdownNow()를 호출한다.
- 호출한 스레드에 인터럽트가 발생해도 shutdownNow()를 호출된다.