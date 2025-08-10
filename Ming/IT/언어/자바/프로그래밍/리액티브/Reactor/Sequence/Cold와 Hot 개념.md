> Cold는 무언가를 새로 시작하고, Hot은 무언가를 새로 시작하지 않는다.

## Cold Sequence
![[Pasted image 20250810155447.png]]
- Subscriber의 구독 시점이 달라도 구독을 할 때마다 Publisher가 데이터를 emit하는 과정을 처음부터 다시 작하는 데이터의 흐름을 **Cold Sequence**라고 부른다.
```java
Flux<String> coldFlux = Flux
	.fromIterable(Arrays.asList("KOREA", "JAPAN", "CHINEESE"))  
	.map(String::toLowerCase);  

coldFlux.subscribe(country -> System.out.println("# 구독자1:" + country));  

Thread.sleep(2000L);  
System.out.println("----------------------------------");  

coldFlux.subscribe(country -> System.out.println("# 구독자2: " + country));
```

```console
# 구독자1:korea
# 구독자1:japan
# 구독자1:chineese
----------------------------------
# 구독자2: korea
# 구독자2: japan
# 구독자2: chineese
```

## Hot Sequence
![[Pasted image 20250810160417.png]]
- Hot Sequence의 경우 구독이발생한 시점 이전에 Publisher로부터 emit된 데이터는 Subscriber가 전달받지 못하고 구독이 발생한 시점 이후에 emit된 데이터만 전달받을 수 있다.
```java
String[] singers = {"가수 A", "가수 B", "가수 C", "가수 D", "가수 F"};  
System.out.println("# Begin concert:");  
Flux<String> concertFlux = Flux.fromArray(singers)  
	.delayElements(Duration.ofSeconds(1))  
	.share();  
	
concertFlux.subscribe(  
	singer -> log.info("# 구독자1가 보고있습니다 " + singer + "의 노래를")  
);  
Thread.sleep(2500);  
  
concertFlux.subscribe(  
	singer -> log.info("# 구독자2가 보고있습니다 " + singer + "의 노래를")  
);
Thread.sleep(3000);
```

```console
16:20:02.552 [parallel-1] # 구독자1가 보고있습니다 가수 A의 노래를
16:20:03.558 [parallel-2] # 구독자1가 보고있습니다 가수 B의 노래를

16:20:04.564 [parallel-3] # 구독자1가 보고있습니다 가수 C의 노래를
16:20:04.565 [parallel-3] # 구독자2가 보고있습니다 가수 C의 노래를

16:20:05.571 [parallel-4] # 구독자1가 보고있습니다 가수 D의 노래를
16:20:05.571 [parallel-4] # 구독자2가 보고있습니다 가수 D의 노래를

16:20:06.577 [parallel-5] # 구독자1가 보고있습니다 가수 F의 노래를
16:20:06.578 [parallel-5] # 구독자2가 보고있습니다 가수 F의 노래를
```
> `parallel-1` 같은 스레드가 실행되는 이유는 `delayElements()` 연산자의 디폴트 스레드 스케줄러가 parallel이기 때문이다.


