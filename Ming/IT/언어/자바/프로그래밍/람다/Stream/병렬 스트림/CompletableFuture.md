> 별도의 스레드 풀을 지정해서 사용할 것!

```java
CompletableFuture.runAsync(() -> log("Fork/Join")); // Fork/Join 공용 풀

ExecutorService es = Executors.newFixedThreadPool(100);
CompletableFuture.runAsync(() -> log("Custom Pool"), es); // 별도의 풀

es.close();
```
- 별도의 스레드 풀을 지정하지 않으면 Fork/Join 공용 풀을 대신 사용한다. 이로 인해 많은 장애가 발생한다.
- 반드시, 커스텀 풀을 지정해서 사용해야한다.