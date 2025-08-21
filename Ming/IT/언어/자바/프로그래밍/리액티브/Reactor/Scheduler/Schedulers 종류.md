## `Schedulers.immediate()`
- 별도의 스레드를 추가적으로 생성하지 않고, 현재 스레드에서 작업을 처리하고자 할 떄 사용(아무 변화 없음)
```java
Flux.fromArray(new Integer[]{1, 2, 3, 4, 5, 6,})  
	.publishOn(Schedulers.parallel())  
	.filter(data -> data > 3)  
	.doOnNext(data -> log.info("# doOnnext filter: {}", data))  
	.publishOn(Schedulers.immediate())  
	.map(data -> data * 10)  
	.doOnNext(data -> log.info("# doOnNext map: {}", data))  
	.subscribe(data -> log.info("# onNext: {}", data));
```
![[Pasted image 20250821235228.png]]

## `Schedulers.single()`
- 스레드 하나만 생성해서 스케줄이 제거되기 전까지 재사용하는 방식
```java
public static void main(String[] args) throws InterruptedException {  
	doTask("task1").subscribe(data -> log.info("# onNext: {}", data));  
	doTask("task2").subscribe(data -> log.info("# onNext: {}", data));  
	  
}  
  
public static Flux<Integer> doTask(String taskName) {  
	return Flux.fromArray(new Integer[]{1, 3, 5, 7})  
	.publishOn(Schedulers.single())  
	.filter(data -> data > 3)  
	.doOnNext(data -> log.info("# {} doOnnext filter: {}", taskName, data))  
	.map(map -> map * 10)  
	.doOnNext(data -> log.info("# {} doOnNext map: {]", taskName, data));  
}
```

![[Pasted image 20250821235733.png]]

## `Schedulers.newSingle()`
- `Schedulers.single()`이 하나의 스레드를 재사용한 반면에 `Schedulers.newSingle()`은 호출 할 때마다 새로운 스레드를 하나 생성한다.
```java
public static void main(String[] args) throws InterruptedException {  
	doTask("task1").subscribe(data -> log.info("# onNext: {}", data));  
	doTask("task2").subscribe(data -> log.info("# onNext: {}", data));  
	  
	Thread.sleep(200L);  
}  
  
public static Flux<Integer> doTask(String taskName) {  
return Flux.fromArray(new Integer[]{1, 3, 5, 7})  
	.publishOn(Schedulers.newSingle("new-single", true))  
	.filter(data -> data > 3)  
	.doOnNext(data -> log.info("# {} doOnnext filter: {}", taskName, data))  
	.map(map -> map * 10)  
	.doOnNext(data -> log.info("# {} doOnNext map: {]", taskName, data));  
}
```
- [[데몬 스레드]]
![[Pasted image 20250822000446.png]]

## `Schedulers.boundedElastic()`
- ExecutorService 기반의 스레드 풀을 생성 그안의 스레드 수만큼 작업을 처리하고 재사용하는 방식
- 블로킹 I/O 작업을 효과적으로 처리하기 위한 방식임

## `Schedulers.parallel()`
- Non-Blocking I/O에 최적화 되어있는 Scheduler로 CPU 코어 수 만큼의 스레드를 생성한다.
- CPU작업에 최적화 되어 있다.

## `Schedulers.fromExecutorService()`
- 기존에 사용중인 ExecutorService가 있다면 이 ExecutorService로 부터 Scheduler를 생성하는 방식

## `Schedulers.newXXXX()`
- Reactor에서 제공하는 디폴트 Scheduler 인스턴스를 사용한다. 하지만 필요하다면 newXXX시리즈를 사용해서 새로운 Scheduler인스턴스를 생성할 수 있다. 
- 스레드를 커스텀 가능
	- 스레드 이름
	- 생성 가능한 디폴트 스레드 개수
	- 스레드 유휴 시간
	- 데몬 스레드로 동작 여부