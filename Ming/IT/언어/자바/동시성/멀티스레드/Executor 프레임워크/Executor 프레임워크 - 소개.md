### 주요 구성 요소
##### Executor 인터페이스
```java
package java.util.concurrent;

public interface Executor {
	void execute(Runnable command);
}
```


##### ExecutorService 인터페이스 - 주요 메서드
```java
public interface ExecutorService extends Executor, AutoCloseable {

	<T> Future<T> submit(Callable<T> task);
	
	@Override
	default void close(){...}

...

}
```
- `Executor` 인터페이스를 확장해서 작업 제출과 제어 기능을 추가로 제공한다.
- 주요 메서드로는 `submit()` , `close()` 가 있다.
- Executor 프레임워크를 사용할 때는 대부분 이 인터페이스를 사용한다.

>`ExecutorService` 인터페이스의 기본 구현체는 `ThreadPoolExecutor` 이다.

---
### 실행 순서
![[Pasted image 20250515170222.png]]
![[Pasted image 20250515170244.png]]

```
17:02:57.614 [     main] == 초기 상태==
17:02:57.661 [     main] [pool=0, active=0, QueuedTasks=0, complateTask=0]
17:02:57.663 [     main] == 작업 수행 중 ==
17:02:57.665 [     main] [pool=2, active=2, QueuedTasks=2, complateTask=0]
```


![[Pasted image 20250515170418.png]]
```
17:03:00.676 [     main] == 작업 수행 완료 ==
17:03:00.679 [     main] [pool=2, active=0, QueuedTasks=0, complateTask=4]
17:03:00.684 [     main] == shutdown ==
17:03:00.693 [     main] [pool=0, active=0, QueuedTasks=0, complateTask=4]
```


---
### 실행코드
##### 기존 코드
```java
ExecutorService es = new ThreadPoolExecutor(1,1,0, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<>());
```

##### 편의 코드
```java
ExecutorService es = Executors.newFixedThreadPool(1);
```