![[Pasted image 20250501164403.png]]
1. 모든 객체(인스턴스)는 자신만의 락을 가지고 있으며 이것을 **모니터 락**이라고 한다.
2. synchronized를 호출하면 해당 인스턴스의 락이 필요하게 된다.

![[Pasted image 20250501164553.png]]

1. 해당 인스턴스 진입을 위해 lock이 필요하지만 없을 경우 해당 스레드는 **BLOCKED** 상태가 된다
2. 락을 획득한 스레드만 **BLOCKED -> RUNNABLE** 상태가 된다.
> - BLOCKED 상태가되면 해당 lock을 획득하기 전까지 CPU 실행 스케줄링에 들어가지 않는다.
> - 어떤 스레드가 락을 획득하는 순서는 보장되지 않는다
> - volatile을 사용하지 않아도 synchronized 안에서 접근하는 변수의 메모리 가시성은 해결 된다.

---

### 필요한 영역만 락을 획득하는 방법 (자바)
```java
@Override
synchronized (this) {
	log("[검증 시작] 출금액: " + amount + ", 잔액: " + balance);
	if (balance < amount) {
		log("[검증 실패] 출금액: " + amount + ", 잔액: " + balance);
		return false;
	}
	log("[검증 완료] 출금액: " + amount + ", 잔액: " + balance);
	sleep(1000);
	balance = balance - amount;
	log("[출금 완료] 출금액: " + amount + ", 변경 잔액: " + balance);
}

```

---

![[Pasted image 20250501173938.png]]
> 지역 변수는 개별 스레드 스택 영역에서 생성되기 때문에 동기화는 필요없다.

---

### synchronized 장점
- 자동 장금 해제
- 단숨함
- 프로그래밍 언어에 문법으로 제공
### synchronized 단점
- **무한 대기** : `BLOCKED` 상태의 스레드는 락이 풀릴 때 까지 무한 대기한다.
	- 특정 시간까지만 대기하는 타임아웃은 지원하지 않는다
	- 중간에 인터럽트는 지원하지 않는다.
- **공정성** : 락이 돌아왔을 때 `BLOCKED` 상태의 여러 스레드 중에 어떤 스레드가 락을 획득할 지 알 수 없다. 최악의 경우 특정 스레드가 너무 오랜기간 락을 획득하지 못 할 수 있다.