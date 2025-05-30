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
- 요청 스레드가 `future.get()` 을 호출하면 `Future` 가 완료 상태가 될 때 까지 대기한다. 이때 요청 스레드의 상태는 `RUNNABLE` `WAITING` 이 된다.
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
