1. **`just()`**
    - 가변 인자 입력 방식.
    - **null 요소 포함 불가** → null 포함 시 `NullPointerException` 발생.
2. **`justOrEmpty()`**
	- null요소 포함 허용
	- null이 전달되면 내부적으로 `empty()`를 호출
3. **`empty()`**
    - emit할 데이터 없이 곧바로 `onComplete` 신호 전송.
    - 데이터는 없지만 작업 종료를 알리고 후처리를 할 때 사용.
4. **`subscribe(consumer, errorConsumer, completeConsumer)`**
    - `consumer`: `onNext` 신호 수신 시 실행. (데이터 처리)
    - `errorConsumer`: `onError` 신호 수신 시 실행. (에러 처리)
    - `completeConsumer`: `onComplete` 신호 수신 시 실행. (종료 후처리)
5. **`fromArray()`**
    - 배열을 Flux로 변환.
    - **null 요소 포함 가능** → null 요소는 emit하지 않음.
6. **`concatWith(Publisher<? extends T> other)`**
	- `Publisher`(Flux/Mono)를 **순차적으로 연결**(concat)해서 실행하는 연산자
	-  에러 발생시 뒤의 Publisher는 실행되지 않음
	-  순서 보장(순차적으로 실행된다 앞의 Publisher실행후 뒤의 Publisher를 구독함)
	-  인스턴스 메서드
7. **`concat()`**
	- 정적 메서드
	- 여러개의 Publisher를 순차적으로 연결해서 실행하는 연산자


---
```java
System.out.println("=== FLUX EX1 ===");  
Flux.just(6, 9, 13)  
	.map(num -> num % 2)  
	.subscribe(System.out::println);  
	  
System.out.println("=== FLUX EX2 ===");  
Flux.fromArray(new Integer[]{3, 6, 7, 9})  
	.filter(num -> num > 6)  
	.map(num -> num * 2)  
	.subscribe(System.out::println);  
  
System.out.println("=== FLUX EX3 ===");  
Flux<String> flux = Mono.justOrEmpty("Steve")  
	.concatWith(Mono.justOrEmpty("Jobs"));  
flux.subscribe(System.out::println);

System.out.println("=== FLUX EX4 ===");  
Flux.concat(  
	Flux.just("mercury", "Venus", "Earth"),  
	Flux.just("Mars", "Jupiter", "Saturn"),  
	Flux.just("Uranus", "Neptune", "Pluto")  
).collect(Collectors.toList())  
.subscribe(System.out::println);
```

```console
=== FLUX EX1 ===
0
1
1

=== FLUX EX2 ===
14
18

=== FLUX EX3 concatWith() ===
Steve
Jobs

=== FLUX EX4 ===
[mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto]
```