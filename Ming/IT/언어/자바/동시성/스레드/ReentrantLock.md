>자바는 1.0부터 존재한 `synchronized` 와 `BLOCKED` 상태를 통한 통한 **임계 영역 관리의 한계**(무한대기)를 극복하기 위해 자바1.5부터 `Lock` 인터페이스와 `ReentrantLock` 구현체를 제공한다


>여기서 사용하는 락은 객체 내부에 있는 모니터 락이 아니다! 
>`Lock` 인터페이스와 `ReentrantLock` 이 제공하는 기능이다! 
>모니터 락과 `BLOCKED` 상태는 `synchronized` 에서만 사용된다.


```java
package java.util.concurrent.locks;

public interface Lock {
	void lock();
	void lockInterruptibly() throws InterruptedException;
	boolean tryLock();
	boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
	void unlock();
	Condition newCondition();
}

```

#### lock() : 
- 락을 획득한다. 만약 다른 스레드가 이미 락을 획득했다면, **락이 풀릴 때까지 현재 스레드는 대기**(`WAITING` )한다. 이 메서드는 **인터럽트에 응답하지 않는다**.
- 인터럽트가 발생하면 순간 대기 상태에서 빠져나오지만 lock() 메서드 안에서 **강제로 WAITING 상태로 변경**해버린다.

