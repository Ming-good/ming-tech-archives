```java
public static void main(String[] args) {  
	List<String> strings = List.of("1", "12", "123", "1234");  
	  
	// 문자열을 숫자로 변환  
	List<Integer> numbers = map(strings, s -> Integer.valueOf(s));  
	System.out.println(numbers);  
	  
	// 문자열의 길이  
	List<Integer> lengths = map(strings, s -> s.length());  
	System.out.println(lengths);  
}  

private static <T,R> List<R> map(List<T> strings, Function<T, R> func) {  
	List<R> numbers = new ArrayList<>();  
	for (T s : strings) {  
		numbers.add(func.apply(s));  
	}  
	return numbers;  
}
```