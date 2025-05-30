```java
package java.util.concurrent;

public interface Callable<V> {
	V call() throws Exception;
}
```
- `java.util.concurrent` 에서 제공되는 기능이다.
- `Callable` 의 `call()` 은 반환 타입이 제네릭 `V` 이다. 따라서 값을 반환할 수 있다.
- `throws Exception` 예외가 선언되어 있다. 따라서 해당 인터페이스를 구현하는 모든 메서드는 체크 예외인 `Exception` 과 그 하위 예외를 모두 던질 수 있다.
> jdk1.5부터 지원 된다.

---
### Callable과 Future 사용법

```java
public class CallableMainV1 {  
  
		public static void main(String[] args) throws ExecutionException, InterruptedException {  
		ExecutorService es = Executors.newFixedThreadPool(1);  		  
		Future<Integer> future = es.submit(new MyCallable());  
		Integer result2 = future.get();  
		log("result value = " + result2);  
		es.close();  
	}  
  
	static class MyCallable implements Callable<Integer> {  
  
		@Override  
		public Integer call() throws Exception {  
			log("Callable 시작");  
			sleep(2000);  
			int value = new Random().nextInt(10);  
			log("create value = " + value);  
			log("Callable 종료");  
			return value;  
		}  
	}  
}
```

##### ExecutorService.submit()
```java
<T> Future<T> submit(Callable<T> task); //인터페이스 정의
```

- `ExecutorService` 가 제공하는 `submit()` 을 통해 `Callable` 을 작업으로 전달할 수 있다.

