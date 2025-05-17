##### invokeAll()
-  `<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException` 
	- 모든 `Callable` 작업을 제출하고, 모든 작업이 완료될 때까지 기다린다.
 - `<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException` 
	- 지정된 시간 내에 모든 `Callable` 작업을 제출하고 완료될 때까지 기다린다.

##### invokeAny()
 - `<T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException` 
	- 하나의 `Callable` 작업이 완료될 때까지 기다리고, 가장 먼저 완료된 작업의 결과를 반환한다. 
	- 완료되지 않은 나머지 작업은 취소한다.
 - `<T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout,TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException` 
	- 지정된 시간 내에 하나의 `Callable` 작업이 완료될 때까지 기다리고, 가장 먼저 완료된 작업의 결과를 반환한다.
	- 완료되지 않은 나머지 작업은 취소한다.