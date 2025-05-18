> WAITING, TIMED_WAITING 상태를 변경할 수 있고 인터럽트를 받아서 깨울 수 있다. 
> 이런 기능들을 잘 활용하면 synchronized의 단점인 **무한 대기 문제**를 해결 할 수 있다.

> LockSupport는 JVM내부적으로 Unsafe.park()를 호출하며 JVM내부에서 OS의 native blocking call을 사용함
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