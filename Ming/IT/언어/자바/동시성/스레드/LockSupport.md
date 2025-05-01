### park() : 스레드를 WAITING 상태로 전환한다.
```java
public void run() {
	log("park 시작");
	LockSupport.park();
	log("park 종료, state: " + Thread.currentThread().getState());
	log("인터럽트 상태: " + Thread.currentThread().isInterrupted());
}
```

### unpark(obj) : WAITING -> RUNNABLE 상태로 전환 




![[Pasted image 20250501204610.png]]


---

### parkNanos(시간) : 지정 시간 이후 스레드가 깨어난다.