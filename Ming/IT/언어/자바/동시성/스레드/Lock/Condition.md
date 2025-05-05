```java
Condition condition = lock.newCondition()
```

#### Condition
- `Condition` 은 `ReentrantLock` 을 사용하는 스레드가 대기하는 스레드 대기 공간이다.
- `lock.newCondition()` 메서드를 호출하면 스레드 대기 공간이 만들어진다. `Lock(ReentrantLock)` 의 스레드 대기 공간은 이렇게 만들 수 있다.
>참고로 `Object.wait()` 에서 사용한 스레드 대기 공간은 모든 객체 인스턴스가 내부에 기본으로 가지고 있다.
>반면에 `Lock(ReentrantLock)`을 사용하는 경우 이렇게 스레드 대기 공간을 직접 만들어서 사용해야 한다.

#### condition.await()
- `Object.wait()` 와 유사한 기능이다. 지정한 `condition` 에 현재 스레드를 대기(`WAITING` ) 상태로 보관한다.
> 이때 `ReentrantLock` 에서 획득한 락을 반납하고 대기 상태로 `condition` 에 보관된다.

#### condition.signal()
- `Object.notify()` 와 유사한 기능이다. 지정한 `condition` 에서 대기중인 스레드를 하나 깨운다. 깨어난 스레드는 `condition` 에서 빠져나온다.

---
![[Pasted image 20250506001802.png]]
- 여기서 lock은 ReentrantLock에 lock이며 스레드 대기 공간은 Condition이다.
