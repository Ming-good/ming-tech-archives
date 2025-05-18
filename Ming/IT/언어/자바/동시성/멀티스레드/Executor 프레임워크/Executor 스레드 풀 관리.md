### ThreadPoolExecutor 생성자의 속성
- corePoolSize` : 스레드 풀에서 관리되는 기본 스레드의 수
- `maximumPoolSize` : 스레드 풀에서 관리되는 최대 스레드 수
- `keepAliveTime` , `TimeUnit unit` : 기본 스레드 수를 초과해서 만들어진 초과 스레드가 생존할 수 있는 대기 시간, 이 시간 동안 처리할 작업이 없다면 초과 스레드는 제거된다.
- `BlockingQueue workQueue` : 작업을 보관할 블로킹 큐

### 스레드 풀, 초과 스레드 생성, 종료 동작
0. 스레드 풀은 **기본 corePoolSize**로 스레드를 운영한다. 
	- 작업 요청마다 corePoolSize만큼 스레드를 생성한다.
1. 요청이 **corePoolSize를 초과**할 경우 큐 사이즈 만큼 요청을 받는다.
2. 만약 **큐 사이즈마저 초과**할 경우 **maximumPoolSize만큼 초과 스레드가 생성**된다. 
3. **maximumPoolSize와 큐 사이즈도 초과**할 경우 **RejectedExecutionException**이 발생한다.
4. **keepAliveTime, TimeUnit** 대기 시간동안 초과 스레드가 처리할 작업이 없다면 **초과 스레드는 제거 된다.**

### 스레드 미리 생성하기
- `ThreadPoolExecutor.prestartAllCoreThreads()` 를 사용하면 기본 스레드를 미리 생성할 수 있다.
```java
public static void main(String[] args) {
	ExecutorService es = Executors.newFixedThreadPool(1000);
	printState(es);
	
	ThreadPoolExecutor poolExecutor = (ThreadPoolExecutor) es;
	poolExecutor.prestartAllCoreThreads();
	printState(es);

}
```
