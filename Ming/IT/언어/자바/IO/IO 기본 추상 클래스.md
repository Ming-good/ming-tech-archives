![[Pasted image 20250525184331.png]]

---
### 메모리 스트림
```java
public static void main(String[] args) throws IOException {
	byte[] input = {1, 2, 3};
	
	// 메모리에 쓰기
	ByteArrayOutputStream baos = new ByteArrayOutputStream();
	baos.write(input);
	
	// 메모리에서 읽기
	ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
	byte[] bytes = bais.readAllBytes();
	
	System.out.println(Arrays.toString(bytes));
}
```
- ByteArrayOutputStream, ByteArrayInputStream을 사용하면 메모리에 스트림을 쓰고 읽을 수 있다.
- 메모리에 어떤 데이터를 저장하고 읽을 때는 컬렉션이나 배열을 사용하면 되기 때문에 이 기능은 잘 사용하지 않는다. 
- 주로 스트림을 간단하게 테스트 하거나 스트림의 데이터를 확인하는 용도로 사용된다.

---
### 콘솔 스트림
```java
public static void main(String[] args) throws IOException {
	PrintStream printStream = System.out;
	byte[] bytes = "Hello!\n".getBytes(UTF_8);
	printStream.write(bytes);
	printStream.println("Print!");
}
```
> System.out은 사실 PrintStream이다. 이 스트림은 OutputStream을 상속 받는다.
> 이 스트림은 자바가 시작될 때 자동으로 만들어 진다.
- `write(byte[])` : OutputStream 부모 클래스가 제공하는 기능
- `println(String)` : PrintStream이 자체적으로 제공하는 추가 기능
