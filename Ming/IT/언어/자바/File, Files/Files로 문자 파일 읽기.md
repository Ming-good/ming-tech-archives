```java
String writeString = "abc\n가나다";  
System.out.println("== Write String ==");  
System.out.println(writeString);  
  
Path path = Path.of(PATH);  
  
// 파일 쓰기  
Files.writeString(path, writeString, StandardCharsets.UTF_8);  
  
// 파일에서 읽기  
System.out.println("== Read String ==");  
List<String> lines = Files.readAllLines(path, StandardCharsets.UTF_8);  
for (int i = 0; i < lines.size(); i++) {  
System.out.println((i+1) + " : " + lines.get(i));  
}  
```
###### `Files.writeString(path, writeString, StandardCharsets.UTF_8)`
-  내부에 버퍼 처리 다 되어 있다.

###### `Files.readAllLines(path)`
- 파일을 한 번에 다 읽고, 라인 단위로 `List` 에 나누어 저장하고 반환한다.

###### `Files.lines(path)`
- 파일을 한 줄 단위로 나누어 읽고, 메모리 사용량을 줄이고 싶다면 이 기능을 사용하면 된다.
- 이 기능을 제대로 이해하려면 람다와 스트림을 알아야 한다.
```java
try(Stream<String> lineStream = Files.lines(path, UTF_8)){
	lineStream.forEach(line -> System.out.println(line));
}
```
>이 기능을 사용하면 파일을 한 줄 단위로 메모리에 올릴 수 있다. 한 줄 당 1MB의 용량을 사용한다면 
>자바는 파일에서 한 번에 1MB의 데이터만 메모리에 올려 처리한다. 
>그리고 처리가 끝나면 다음 줄을 호출하고, 기존에 사용한 1MB의 데이터는 GC한다.

