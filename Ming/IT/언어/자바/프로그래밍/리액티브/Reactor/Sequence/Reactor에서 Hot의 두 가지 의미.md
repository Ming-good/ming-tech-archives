## `Cache()`로 HOT Sequence
```java
URI uri = UriComponentsBuilder.newInstance().scheme("http")  
	.host("127.0.0.1")  
	.port(7070)  
	.path("/v/books/1")  
	.build()  
	.encode()  
	.toUri();  
  
Mono<String> mono = getBook(uri).cache();  
mono.subscribe(name -> log.info("# Book1 name: {}", name));  
Thread.sleep(1000);  
mono.subscribe(name -> log.info("# Book2 name: {}", name));  
Thread.sleep(3000);
```

```console
17:08:32.188 [reactor-http-nio-2] - # Book1 name: IT Book1
17:08:32.188 [reactor-http-nio-2] - # Book2 name: IT Book1

시간이 동일하다. 왜냐하면 캐싱된 데이터를 출력함
```
![[Pasted image 20250810171555.png]]
