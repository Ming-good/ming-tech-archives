>자바는 1.0부터 존재한 `synchronized` 와 `BLOCKED` 상태를 통한 통한 **임계 영역 관리의 한계**(무한대기)를 극복하기 위해 자바1.5부터 `Lock` 인터페이스와 `ReentrantLock` 구현체를 제공한다


>여기서 사용하는 락은 객체 내부에 있는 모니터 락이 아니다! 
>`Lock` 인터페이스와 `ReentrantLock` 이 제공하는 기능이다! 
>**모니터 락과 `BLOCKED`** 상태는 **`synchronized`** 에서만 사용된다.


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


---
## 공정성 :
> 락이 돌아왔을 때 BLOCKED 여러 스레드 중에 어떤 스레드가 락을 획을할 지 알 수 없다.

```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockEx {

	// 비공정 모드 락
	private final Lock nonFairLock = new ReentrantLock();
	
	// 공정 모드 락
	private final Lock fairLock = new ReentrantLock(true);
```
#### 비공정 **모드** (Non-fair mode)
>비공정 모드는 `ReentrantLock` 의 기본 모드이다. 이 모드에서는 락을 먼저 요청한 스레드가 락을 먼저 획득한다는 보장이 없다. 락을 풀었을 때, 대기 중인 스레드 중 아무나 락을 획득할 수 있다. 이는 락을 빨리 획득할 수 있지만, 특정 스레드가 장기간 락을 획득하지 못할 가능성도 있다.
###### 비공정 모드 특징
- **성능 우선** : 락을 획득하는 속도가 빠르다.
- **선점 가능** : 새로운 스레드가 기존 대기 스레드보다 먼저 락을 획득할 수 있다.
- **기아 현상 가능성** : 특정 스레드가 계속해서 락을 획득하지 못할 수 있다.

#### 공정 모드 (Fair mode)
>생성자에서 `true` 를 전달하면 된다. 예) `new ReentrantLock(true)` 
>공정 모드는 락을 요청한 순서대로 스레드가 락을 획득할 수 있게 한다. 이는 먼저 대기한 스레드가 먼저 락을 획득하게 되어 스레드 간의 공정성을 보장한다. 그러나 이로 인해 성능이 저하될 수 있다.
###### 공정 모드 특징
- **공정성 보장** : 대기 큐에서 먼저 대기한 스레드가 락을 먼저 획득한다.
- **기아 현상 방지** : 모든 스레드가 언젠가 락을 획득할 수 있게 보장된다.
- **성능 저하** : 락을 획득하는 속도가 느려질 수 있다.


---
## 내부 실행 과정
![[Pasted image 20250501220009.png]]


![[Pasted image 20250501220055.png]]

![[Pasted image 20250501220238.png]]
> `volatile`를 사용하지 않아도 `Lock` 을 사용할 때 접근하는 변수의 메모리 가시성 문제는 해결된다. 
> (이전에 학습한 [[자바 메모리 모델(JMM)]] 참고)

---

