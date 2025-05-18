- **newSingleThreadPool()**: 단일 스레드 풀 전략
	- 스레드 1개 사용
	- 주로 간단히 사용하거나, 테스트 용도
- **newFixedThreadPool(nThreads)**: 고정 스레드 풀 전략
- **newCachedThreadPool()**: 캐시 스레드 풀 전략
---
### 고정 풀 전략
```java
new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())
```

##### 특징
- 스레드 수가 고정되어 있기 때문에 CPU, 메모리 리소스가 어느정도 예측 가능한 안정적인 방식이다. 큐 사이즈도 제한이 없어서 작업을 많이 담아두면 문제가 없다.

##### 주의
- 스레드 수가 고정되어서 **점진적인 사용자 확대**나 **갑작스런 요청 증가** 상황의 경우 큰 단점이 된다.