
```java
StepVerifier  
	.create(Mono.just("Hello"))  
	.expectNext("Hello")  
	.expectComplete()  
	.verify();
```

```java
StepVerifier  
	.create(GetneralTestExample.sayHello())  
	.expectSubscription()  
	.as("# expect subscription")  
	.expectNext("Hi")  
	.as("# expect Hi")  
	.expectNext("Reactor")  
	.as("# expect Reactor")  
	.verifyComplete();
```

```console
java.lang.AssertionError: expectation "# expect Hi" failed (expected value: Hi; actual value: Hello)

```
- `as()`: 이전 기댓값 평가 단계에 대한 설명 추가


```java
Flux<Integer> source = Flux.just(2,4,6,8,10);  
StepVerifier  
	.create(GetneralTestExample.divideByTwo(source))  
	.expectSubscription()  
	.expectNext(1)  
	.expectNext(2)  
	.expectNext(3)  
	.expectNext(4)  
	// .expectNext(1, 2, 3, 4)  
	.expectError()  
	.verify();
	
/** 5번 째 emit에서 0으로 나누어서 오류 발생 예측 **/
```

```java
Flux<Integer> source = Flux.range(0, 1000);  
StepVerifier  
	.create(GetneralTestExample.takeNumber(source, 500), StepVerifierOptions.create().scenarioName("Verify from 0 to 499"))  
	.expectSubscription()  
	.expectNext(0)  
	.expectNext(1)  
	.expectNextCount(497)  
	.expectNext(499)  
	.expectComplete()  
	.verify();
```
- `expectNextCount()`:  앞의 값들을 평가하고 이후 총 `emit()`된 개수를 기대