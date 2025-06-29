```java
public static <T> List<T> filter(List<T> numbers, Predicate<T> predicate) {  
	List<T> filtered = new ArrayList<>();  
	for (T number : numbers) {  
		if (predicate.test(number)) {  
			filtered.add(number);  
		}  
	}  
	return filtered;  
}  
```


```java
public static void main(String[] args) {  
	List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);  
	List<Integer> even = filter(numbers, v -> v % 2 == 0);  
	System.out.println(even);  
	  
	List<String> strings = List.of("A", "BB", "CCC");  
	List<String> stringResult = filter(strings, s -> s.length() >= 2);  
	System.out.println(stringResult);  
}
```