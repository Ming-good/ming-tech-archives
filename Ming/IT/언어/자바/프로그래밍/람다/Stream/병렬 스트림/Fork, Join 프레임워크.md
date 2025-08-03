

![[Pasted image 20250729222459.png]]

## `RecursiveTask<>` 사용 예시 - 분할 정복 전략
- 큰 작업을 작은 단위로 재귀적으로 분할(fork)
- 멀티코어 환경에서 작업을 효율적으로 분산 처리
`RecursiveTask 구현 예시`
```java
public class SumTask extends RecursiveTask<Integer> {  
	private static final int THRESHOLD = 4;  
	private final List<Integer> list;  
  
	public SumTask(List<Integer> list) {  
		this.list = list;  
	}  
  
	@Override  
	protected Integer compute() {  
		// 작업 범위가 작으면 직접 계산  
		// [5 ~ 8]  
		if (list.size() <= THRESHOLD) {  
			MyLogger.log("[처리 시작] " + list);  
			int sum = list.stream()  
				.mapToInt(HeavyJob::heavyTask)  
				.sum();  
			MyLogger.log("[처리 완료] " + list + " -> sum: " + sum);  
			return sum;  
		} else {  
			int mid = list.size()/2;  
			List<Integer> leftList = list.subList(0, mid);  
			List<Integer> rightList = list.subList(mid, list.size());  
			MyLogger.log("[분할] " + list + " -> LEFT" + leftList + ", RIGHT" + rightList);  
			  
			SumTask leftTask = new SumTask(leftList);  
			SumTask rightTask = new SumTask(rightList);  
			  
			// 왼쪽 작업은 다른 스레드에서 처리  
			leftTask.fork();  
			  
			// 오른쪽 작업은 현재 스레드에서 처리  
			Integer rightResult = rightTask.compute();  
			  
			// 왼쪽 작업 결과를 기다림  
			Integer leftResult = leftTask.join();  
			int joinSum = leftResult + rightResult;  
			MyLogger.log("LEFT[" + leftResult + "] + RIGHT["+rightResult+"] -> sum : " + joinSum);  
			return joinSum;  
		}  
	}  
}
```

`호출`
```java
public static void main(String[] args) {  
	List<Integer> data = IntStream.rangeClosed(1, 8)  
		.boxed()  
		.toList();  
	MyLogger.log("[생성 ]" + data);  
	  
	// ForkJoinPool 생성 및 작업 수행  
	long startTime = System.currentTimeMillis();  
	ForkJoinPool pool = new ForkJoinPool(10);  
	SumTask sumTask = new SumTask(data);  
	  
	// 병렬로 합을 구한 후 결과 출력  
	Integer result = pool.invoke(sumTask);  
	pool.close();  
	long endTime = System.currentTimeMillis();  
	log("time: " + (endTime - startTime) + "ms, sum:" + result);  
	MyLogger.log("pool : " + pool);  
}
```

## 작업 훔치기
- `RecursiveTask.pork()`는 실제로는 새로운 스레드에게 작업을 시키는 명령어가 아니다. 본인 스레드 큐에 작업을 넣어두면 남는 스레드가 작업을 아래부터 steal해가는 것이다.
![[Pasted image 20250731225435.png]]
![[Pasted image 20250731225445.png]]