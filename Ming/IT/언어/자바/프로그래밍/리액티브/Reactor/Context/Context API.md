![[Pasted image 20250824141735.png]]

![[Pasted image 20250824170224.png]]
```java
Mono.deferContextual(  
ctx -> Mono.just(ctx.get(key1) + ", " + ctx.getOrEmpty(key2).orElse("no firstName") + ", " + ctx.getOrDefault(key3, "no lastName")))  
	.publishOn(Schedulers.parallel())  
	.contextWrite(context -> context.put(key1, "Apple"))  
	.subscribe(data -> log.info("# onNext: {}", data));
```