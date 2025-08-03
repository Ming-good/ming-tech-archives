```java
int pct = Runtime.getRuntime().availableProcessors();  
ForkJoinPool commonPool = ForkJoinPool.commonPool();  
log("processorCount = " + pct + ", commonPool = " + commonPool.getParallelism());  
  
long startTime = System.currentTimeMillis();  
int sum = IntStream.rangeClosed(1, 8)  
	.parallel()  
	.map(HeavyJob::heavyTask)  
	.reduce(0, (a, b) -> a + b);  
long endTime = System.currentTimeMillis();  
  
log("time: " + (endTime - startTime) + "ms, sum:" + sum);
```

```console
10:34:25.864 [     main] processorCount = 10, commonPool = 9
10:34:25.883 [ForkJoinPool.commonPool-worker-6] calcculate 7 -> 70
10:34:25.887 [ForkJoinPool.commonPool-worker-3] calcculate 4 -> 40
10:34:25.883 [ForkJoinPool.commonPool-worker-1] calcculate 3 -> 30
10:34:25.883 [ForkJoinPool.commonPool-worker-4] calcculate 8 -> 80
10:34:25.883 [ForkJoinPool.commonPool-worker-5] calcculate 1 -> 10
10:34:25.883 [ForkJoinPool.commonPool-worker-7] calcculate 5 -> 50
10:34:25.887 [     main] calcculate 6 -> 60
10:34:25.887 [ForkJoinPool.commonPool-worker-2] calcculate 2 -> 20
10:34:26.918 [     main] time: 1027ms, sum:360
```
- 직접 스레드를 만들 필요 없이 스트림에 `parallel()`메서드만 호출하면, 스트림이 자동으로 병렬 처리 힌다.
#### 내부 구동 원리
- 스트림에서 `parallel()`를 선언하면 스트림은 공용 `ForkJoinPool`을 사용한다. 
- 내부적으로 병렬 처리 가능한 스레드 숫자와 작업의 크기 등을 확인하면서 `Spliterator`를 통해 데이터를 자동으로 분할한다.
- 분할 방식은 데이터 소스의 특성에 따라 최적화되어 있다.
- 공용 풀을 통해 작업을 적절한 수준으로 분할, 처리, 결과를 모은다.

---
## 병렬 스트림 사용시 주의사항1
> 실무에서 거이 사용 안 함.
- Fork/Join 공용 풀을 사용한다. Fork/Join 공용 풀은 **CPU 바운드 작업(계산 집약적인 작업)을 위해 설계**되었다. 따라서 **I/O바운드 작업에는 적합하지 않다.**
	- I/O 바운드 작업은 주로 네트워크 호출을 통한 대기가 발생한다. 
	- 예) 외부 API 호출, 데이터베이스 조회
#### 문제 상황
1. **스레드 블로킹에 따른 CPU낭비**
	- `ForkJoinPool`은 한정된 스레드만 사용한다.
	- I/O작업은 스레드를 블로킹 시켜서 CPU를 놀게 만든다.
2. **컨텍스트 스위칭 오버헤드 증가**
	- I/O 작업으로 스레드를 늘리면 컨텍스트 스위칭 비용도 증가하여 성능이 오히려 떨어짐
3. **작업 훔치기 기법 무력화**
	- `ForkJoinPool`이 제공하는 작업 훔치기 알고리즘은 CPU바운드 작업에서 빠르게 작업 단위를 처리하도록 설계되어있다.(작업을 훔쳐서 쉬는 스레드 없이 계속 작업)
	- I/O대기 시간이 많은 작업은 병렬 처리의 이점을 살리기 어렵다
4. **분할-정복(작업 분할) 이점 감소**
	- 작업을 나누어도 I/O 병목으로 `fork()`, `join()`에 따른 오버헤드만 증가할 수 있다.

`예시`
```java
public static void main(String[] args) throws InterruptedException {  
	System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "3");  
	// 요청 풀 추가  
	ExecutorService requestPool = Executors.newFixedThreadPool(100);  
	int nThreads = 5; // 1,2,3,10,20  
	for (int i = 0; i < nThreads; i++) {  
		String reqNm = "req" + i;  
		requestPool.submit(() -> logic(reqNm));  
	}  
	requestPool.close();  
}  
  
private static void logic(String reqNm) {  
	log("[" + reqNm + "] START");  
	long startTm = System.currentTimeMillis();  
	int sum = IntStream.rangeClosed(1, 4)  
		.parallel()  
		.map(i -> HeavyJob.heavyTask(i, reqNm))  
		.reduce(0, (a, b) -> a + b);  
	long endTm = System.currentTimeMillis();  
	  
	log("time: " + (endTm - startTm) + ", sum = " + sum);  
}
```
#### 핵심 문제
- **공용 풀 병목 현상**: I/O로 인해서 공용 풀이 모두 사용되어서 다른 스레드 작업들이 대기하게 됨.
- **자원 경쟁**: 요청이 제한된 스레드 풀을 두고 경쟁하면서 요청 성능이 저하된다.
- 예측 불가능한 성능: 같은 작업이라도 동시에 실행되는 다른 작업의 수에 따라 처리 시간이 달라진다. 
#### I/O대기가 있는 작업의 경우 스레드수 계산법
- `스레드 수 ≈ 코어 수×(1 +  IO 대기 시간​ / CPU 작업 시간)`
	- I/O대기 시간 만큼 CPU 작업 시간 분량의 스레드 생성
	- CPU 바운드 공식 스레드 계산은 `1 + 코어수` 만큼임
- 스레드 수를 무작정 많이 늘리면 컨텍스트 스위칭 오버헤드가 발생함.
	- 따라서 이벤트 루프방식은 CPU 캐시 히트율이 올라가서 메모리와 컨텍스트 스위칭 코스트를 낮추어쥼.

---
