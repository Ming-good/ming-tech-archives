- 자바는 멀티스레드 상황에서 안전하게 증가 연산을 수행할 수 있는`AtomicInteger` 라는 클래스를 제공한다. 이름 그대로 원자적인 `Integer` 라는 뜻이다.
> AtomicInteger`가 제공하는 `incrementAndGet()`메서드는 락을 사용하지 않고, 원자적 연산을 만들어낸다.