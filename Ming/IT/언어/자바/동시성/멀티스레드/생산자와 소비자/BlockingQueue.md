> 내부적으로 [[ReentrantLock]]와 [[Condition]] 객체를 이용하여 [[생산자와 소비자 문제]]를 해결한 객체

```java
package java.util.concurrent;

public interface BlockingQueue<E> extends Queue<E> {

	boolean add(E e); // 큐가 가득찰 경우 IllegalStateException 발생
	
	boolean offer(E e);
	
	void put(E e) throws InterruptedException;
	
	boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;

	E take() throws InterruptedException;
	
	E poll(long timeout, TimeUnit unit) throws InterruptedException;
	
	boolean remove(Object o); // 큐가 비어있을 경우 NoSuchElementException 발생
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
### BlockingQueue의 기능
![[Pasted image 20250506195758.png]]

##### Throws Exception - 대기시 예외
- **add(e)**: 지정된 요소를 큐에 추가하며, 큐가 가득 차면 `IllegalStateException` 예외를 던진다.
- **remove()**: 큐에서 요소를 제거하며 반환한다. 큐가 비어 있으면 `NoSuchElementException` 예외를 던진다.
- **element()**: 큐의 머리 요소를 반환하지만, 요소를 큐에서 제거하지 않는다. 큐가 비어 있으면 `NoSuchElementException` 예외를 던진다.
##### Special Value - 대기시 즉시 반환
- **offer(e)**: 지정된 요소를 큐에 추가하려고 시도하며, 큐가 가득 차면 `false` 를 반환한다.
- **poll()**: 큐에서 요소를 제거하고 반환한다. 큐가 비어 있으면 `null` 을 반환한다.
- **peek()**: 큐의 머리 요소를 반환하지만, 요소를 큐에서 제거하지 않는다. 큐가 비어 있으면 `null` 을 반환한다.
##### Blocks - 대기
- **put(e)**: 지정된 요소를 큐에 추가할 때까지 대기한다. 큐가 가득 차면 공간이 생길 때까지 대기한다.
- **take()**: 큐에서 요소를 제거하고 반환한다. 큐가 비어 있으면 요소가 준비될 때까지 대기한다.
##### Times Out - 시간 대기
- **offer(e, time, unit)**: 지정된 요소를 큐에 추가하려고 시도하며, 지정된 시간 동안 큐가 비워지기를 기다리다가시간이 초과되면 `false` 를 반환한다.
- **poll(time, unit)**: 큐에서 요소를 제거하고 반환한다. 큐에 요소가 없다면 지정된 시간 동안 요소가 준비되기를 기다리다가 시간이 초과되면 `null` 을 반환한다.

>참고로 `BlockingQueue` 의 모든 **대기, 시간 대기 메서드**는 인터럽트를 제공한다.