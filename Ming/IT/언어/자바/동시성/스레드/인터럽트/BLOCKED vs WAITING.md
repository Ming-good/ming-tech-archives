### 인터럽트
- `BLOCKED` 상태는 인터럽트가 걸려도 대기 상태를 빠져나오지 못한다. 여전히 `BLOCKED` 상태이다.
- `WAITING` , `TIMED_WAITING` 상태는 인터럽트가 걸리면 대기 상태를 빠져나온다. 그래서 `RUNNABLE` 상태로 변한다.

### 용도
- `BLOCKED` 상태는 자바의 `synchronized` 에서 락을 획득하기 위해 대기할 때 사용된다.
- `WAITING` , `TIMED_WAITING` 상태는 스레드가 **특정 조건**이나 **시간 동안 대기**할 때 발생하는 상태이다.
- `WAITING` 상태는 다양한 상황에서 사용된다. 예를 들어, `Thread.join()` , `LockSupport.park()` ,`Object.wait()` 와 같은 메서드 호출 시 `WAITING` 상태가 된다.
- `TIMED_WAITING` 상태는 `Thread.sleep(ms),` `Object.wait(long timeout)` ,`Thread.join(long millis)` , `LockSupport.parkNanos(ns)` 등과 같은 시간 제한이 있는 대기 메서드를 호출할 때 발생한다.

### 요약
>`BLOCKED` , `WAITING` , `TIMED_WAITING` 상태 모두 스레드가 대기하며, 실행 스케줄링에 들어가지 않기 
> 때문에,CPU 입장에서 보면 실행하지 않는 비슷한 상태이다.
- `BLOCKED` 상태는 `synchronized` 에서만 사용하는 특별한 대기 상태라고 이해하면 된다.
- `WAITING` , `TIMED_WAITING` 상태는 범용적으로 활용할 수 있는 대기 상태라고 이해하면 된다.