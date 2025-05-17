```java
Future<Integer> future = es.submit(new MyCallable());
```

- Callable.call()를 통한 결과를 즉시 받는 것은 불가능하다. 따라서 결과를 나중에 받을 수 있는 Future 객체를 제공한다.

---
### 작동 흐름
![[Pasted image 20250515192221.png]]

![[Pasted image 20250515192300.png]]
- Future 객체는 즉시 반환되고 해당 상태는 Not compleated상태가 된다.

![[Pasted image 20250515192536.png]]
![[Pasted image 20250515192644.png]]
##### 스레드1
- task1작업을 처리중. 아직 완료되지 않았다.
##### 요청 스레드
- 요청 스레드가 `future.get()` 을 호출하면 `Future` 가 완료 상태가 될 때 까지 대기한다. 이때 요청 스레드의 상태는 `RUNNABLE` → `WAITING` 이 된다.
##### futer.get()을 호출 했을 때
- **Future가 완료 상태** : `Future` 가 완료 상태면 `Future` 에 결과도 포함되어 있다. 이 경우 요청 스레드는 대기하지 않고, 값을 즉시 반환받을 수 있다.
- **Future가 완료 상태가 아님** : `taskA` 가 아직 수행되지 않았거나 또는 수행 중이라는 뜻이다. 이때는 어쩔 수 없이 요청 스레드가 결과를 받기 위해 **대기해야 한다**. 요청 스레드가 마치 락을 얻을 때처럼, 결과를 얻기 위해 대기한다. 이처럼 스레드가 어떤 결과를 얻기 위해 대기하는 것을 **블로킹(Blocking)**이라 한다.
> [[블로킹 메서드]]

![[Pasted image 20250515193415.png]]
##### 요청 스레드
- 대기(WAITING) 상태로 future.get()을 호출하고 대기중
##### 스레드1
1. taskA작업 완료한다.
2. Future에 taskA의 반환 결과를 담는다.
3. Future의 상태를 완료로 변경한다.
4. 요청 스레드를 꺠운다. 요청 스레드는 WAITTING → RUNNABLE 상태로 변한다.

> 모든 작업이 완료후 스레드1은 RUNNABLE → WAITTING 상태로 변한다.

---
### 요청 스레드가 WAITING되는 흐름
1. 요청 스레드가 future.get() → park() ← WAITING 상태로 진입
2. park() 전에 WaitNode(thread=요청 스레드)를 waiters에 등록
3. 다른 워커 스레드가 작업 실행: es.submit(task) → FutureTask.run()
4. 작업 끝나면 finishCompletion()에서 waiters에 있는 요청 스레드들 unpark()
5. 요청 스레드 깨어나서 결과 리턴

---

### 주요 메서드
##### boolean cancel(boolean mayInterruptIfRunning)
- **기능** : 아직 완료되지 않은 작업을 취소한다.
- **매개변수** : mayInterrupIfRunning
	- cancle(true) : Futre를 취소 상태로 변경한다. 이때 작업이 실행중이라면 Thread.interrupt()을 호출해서 작업을 중단한다.
	- cancel(false) : Future를 취소 상태로 변경한다. 단 이미 실행중인 작업을 중단하지는 않는다.
- **반환값** : 작업이 성공적으로 취소된 경우true, 이미 완료되었거나 취소할 수 없는 경우 false
- **설명** : 작업이 실행 중이 아니거나 아직 시작되지 않았으면 취소하고, 실행 중인 작업의 경우 mayInterruptIfRunning이 true이면 중단한다.
- **참고** : 취소 상태의 Future에 Future.get()을 호출하면 CancellationException런타임 예외가 발생한다.
##### boolean isCancelled()
- **기능** : 작업이 취소되었는지 여부를 확인한다.
- **반환값** : 작업이 취소된 경우 `true` , 그렇지 않은 경우 `false`
- **설명**: 이 메서드는 작업이 `cancel()` 메서드에 의해 취소된 경우에 `true` 를 반환한다.
##### boolean isDone()
- **기능** : 작업이 완료되었는지 여부를 확인한다.
- **반환값** : 작업이 완료된 경우 `true` , 그렇지 않은 경우 `false`
- **설명** : 작업이 정상적으로 완료되었거나, 취소되었거나, 예외가 발생하여 종료된 경우에 `true` 를 반환한다.
##### State state()
- **기능** : `Future` 의 상태를 반환한다. **자바19**부터 지원한다.
	- `RUNNING` : 작업 실행 중
	- `SUCCESS` : 성공 완료
	- `FAILED` : 실패 완료
	- `CANCELLED` : 취소 완료

##### V get()
- **기능** : 작업이 완료될 때까지 대기하고, 완료되면 결과를 반환한다.
- **반환값** : 작업의 결과
- **예외**
	- `InterruptedException` : 대기 중에 현재 스레드가 인터럽트된 경우 발생
	- `ExecutionException` : 작업 계산 중에 예외가 발생한 경우 발생
- **설명**: 작업이 완료될 때까지 `get()` 을 호출한 현재 스레드를 대기(블록킹)한다. 작업이 완료되면 결과를 반환한다.
##### V get(long timeout, TimeUnit unit)
- **기능** : `get()` 과 같은데, 시간 초과되면 예외를 발생시킨다.
- **매개변수** :
	- `timeout` : 대기할 최대 시간
	- `unit: timeout` 매개변수의 시간 단위 지정
- **반환값** : 작업의 결과
- **예외** :
	- `InterruptedException` : 대기 중에 현재 스레드가 인터럽트된 경우 발생
	- `ExecutionException` : 계산 중에 예외가 발생한 경우 발생
	- `TimeoutException` : 주어진 시간 내에 작업이 완료되지 않은 경우 발생
- **설명** : 지정된 시간 동안 결과를 기다린다. 시간이 초과되면 `TimeoutException` 을 발생시킨다.