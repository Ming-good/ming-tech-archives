>자바 8에서는 공용 풀(Common Pool)이라는 개념이 도입되었는데, 이는 Fork/Join 작업을 위한 자바가 제공하는 기본 스레드 풀이다.

```java
// 자바 8 이상에서는 공용 풀(common pool) 사용 가능
ForkJoinPool commonPool = ForkJoinPool.commonPool();
```

#### Fork/Join 공용 풀의 특징
1. **시스템 전체에서 공유**: 애플리케이션 내에서 **단일 인스턴스로 공유**되어 사용된다.
2. **자동 생성**: 별도로 생성하지 않아도 `ForkJoinPool.commonPool()` 을 통해 접근할 수 있다.
3. **편리한 사용**: 별도의 풀을 만들지 않고도 `RecursiveTask` /`RecursiveAction` 을 사용할 때 기본적으로 이 공용 풀이 사용된다.
4. **병렬 스트림 활용**: 자바 8의 병렬 스트림은 내부적으로 이 공용 풀을 사용한다. (뒤에서 설명한다.)
5. **자원 효율성**: 여러 곳에서 별도의 풀을 생성하는 대신 공용 풀을 사용함으로써 시스템 자원을 효율적으로 관리할 수 있다.
6. **병렬 수준 자동 설정**: 기본적으로 시스템의 가용 프로세서 수에서 1을 뺀 값으로 병렬 수준(parallelism)이 설정된다. 예를 들어 CPU 코어가 14개라면 13개의 스레드가 사용된다. (자세한 이유는 뒤에서 설명)

#### 스레드 수
`processorCount = 14, commonPool = 13`
- 현재 CPU코어가 14개이다. 따라서 공용 풀은 CPU - 1의 수 만큼 스레드를 생성한다.

---
`예제`
```java
List<Integer> data = IntStream.rangeClosed(1, 8)  
	.boxed()  
	.toList();  
log("[생성 ]" + data);  
SumTask sumTask = new SumTask(data);  

//공용 풀 사용  
Integer result = sumTask.invoke();  
log("최종결과: " + result);
```