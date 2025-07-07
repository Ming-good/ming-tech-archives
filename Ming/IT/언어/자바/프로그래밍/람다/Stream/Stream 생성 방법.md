```java
System.out.println("1. 컬렉션으로부터 생성");  
List<String> list = List.of("a", "b", "c");  
Stream<String> stream = list.stream();  
stream.forEach(System.out::println);  
  
System.out.println("2. 배열로부터 생성");  
String[] arr = {"a", "b", "c"};  
Stream<String> stream1 = Arrays.stream(arr);  
stream1.forEach(System.out::println);  
  
System.out.println("3. Stream.of()");  
Stream<String> stream2 = Stream.of("a", "b", "c");  
stream2.forEach(System.out::println);  
  
System.out.println("4. 무한 스트림 생성 - iterate()");
Stream<Integer> infiniteStream = Stream.iterate(0, n -> n + 2);  
infiniteStream.limit(3).forEach(System.out::println);  
  
System.out.println("5. 무한 스트림 생성 - generate()"); 
Stream<Double> randomStream = Stream.generate(Math::random);  
randomStream.limit(3).forEach(System.out::println);
```