> 내부적으로 [[ReentrantLock]]와 [[Condition]] 객체를 이용하여 [[생산자와 소비자 문제]]를 해결한 객체

```java
package java.util.concurrent;

public interface BlockingQueue<E> extends Queue<E> {

	boolean add(E e);
	
	boolean offer(E e);
	
	void put(E e) throws InterruptedException;
	
	boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;

	E take() throws InterruptedException;
	
	E poll(long timeout, TimeUnit unit) throws InterruptedException;
	
	boolean remove(Object o);
	//...

}
```

---
### `BlockingQueue` 인터페이스의 대표적인 구현체
- `ArrayBlockingQueue` : 배열 기반으로 구현되어 있고, 버퍼의 크기가 고정되어 있다.
- `LinkedBlockingQueue` : 링크 기반으로 구현되어 있고, 버퍼의 크기를 고정할 수도, 또는 무한하게 사용할 수도 있다.

##### ArrayBlockingQueue 내부 구조
```java
public class ArrayBlockingQueue {

	final Object[] items;
	
	int count;
	ReentrantLock lock;
	Condition notEmpty; //소비자 스레드가 대기하는 condition
	Condition notFull; //생산자 스레드가 대기하는 condition

	public void put(E e) throws InterruptedException {
	
		lock.lockInterruptibly();
		try {
			while (count == items.length) {
				notFull.await();
			}
			enqueue(e);
		} finally {
			lock.unlock();
		}

	}

	private void enqueue(E e) {
		items[putIndex] = e;
		count++;
		notEmpty.signal();
	}

}
```

---
