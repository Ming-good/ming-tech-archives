## Collectors 기능
1. `toList()`, `toUnmodifiableList()`
```java
// 기본 기능  
List<String> list = Stream.of("Java", "Spring", "JPA")  
.collect(Collectors.toList());  
  
// 수정 불가능 리스트  
List<Integer> unmodifiableList = Stream.of(1, 2, 3)  
.collect(Collectors.toUnmodifiableList());  
```
2.  `toSet()`, `toCollection(HashSet:new)`
```java
Set<Integer> set = Stream.of(1, 2, 2, 3, 3, 3)  
.collect(Collectors.toSet());  
  
// 타입 지정  
TreeSet<Integer> treeSet = Stream.of(3, 4, 5, 2, 1)  
.collect(Collectors.toCollection(TreeSet::new));// TreeSet은 정렬 상태를 유지  
```
3. `toMap(key, val)` : key중복시 오류 발생하며 대안으로는 병합이 있음.
```java
// 기본
Map<String, Integer> map1 = Stream.of("Apple", "Banana", "Tomato")  
.collect(Collectors.toMap(  
	name -> name,  
	name -> name.length()  
));  
  
// 키 중복 대안 (병합)  
Map<String, Integer> map3 = Stream.of("Apple", "Apple", "Tomato")  
	.collect(Collectors.toMap(  
		name -> name,  
		name -> name.length(),  
		(oldVal, newVal) -> oldVal + newVal  
	));  
  
// Map 타입 지정  
Map<String, Integer> map4 = Stream.of("Apple", "Apple", "Tomato")  
	.collect(Collectors.toMap(  
		name -> name,  
		name -> name.length(),  
		(oldVal, newVal) -> oldVal + newVal, // 중복될 경우 기존 값 + 새 값  
		LinkedHashMap::new  
	));  
```
4. 그룹화: `groupingBy()`
```java
// 첫 글자 알파벳을 기준으로 그룹화  
List<String> names = List.of("Apple", "Avocado", "Banana", "Blueberry", "Cherry");  
Map<String, List<String>> grouped = names.stream()  
	.collect(
		Collectors.groupingBy(name -> name.substring(0, 1))
	);  
```
5. 분할: `partitioningBy()`
```java
// 짝수(even)인지 여부로 분할(파티셔닝)  
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);  
Map<Boolean, List<Integer>> partitioned = numbers.stream()  
	.collect(
		Collectors.partitioningBy(n -> n % 2 == 0)
	);  
```
6. 통계: `counting()`, `summingInt()`, `averagingInt()`, `summarizingInt()`
```java
// 다운스트림 컬렉터에서 유용하게 사용  
Integer max1 = Stream.of(1, 2, 3)  
		.collect(Collectors.maxBy(  
		(i1, i2) -> i1.compareTo(i2)  
	)).get();  
  
Integer max2 = Stream.of(1, 2, 3 )  
	.max((i1, i2) -> i1.compareTo(i2))  
	.get();  
  
Integer max3 = Stream.of(1, 2, 3)  
	.max(Integer::compareTo)  
	.get();  
  
int max4 = IntStream.of(1, 2, 3)  
	.max()  
	.getAsInt();  


// 다운스트림 컬렉터에서 사용  
Long count1 = Stream.of(1, 2, 3)  
	.collect(Collectors.counting());  
  
long count2 = Stream.of(1, 2, 3)  
	.count();  
  
Double average1 = Stream.of(1, 2, 3)  
	.collect(Collectors.averagingInt(i -> i));  


// 기본형 특화 스트림으로 변환  
double average2 = Stream.of(1, 2, 3)  
	.mapToInt(i -> i)  
	.average()  
	.getAsDouble();  
  
// 기본형 특화 스트림 사용  
double average3 = IntStream.of(1, 2, 3)  
	.average()  
	.getAsDouble();  
  
// 통계  
IntSummaryStatistics stats = Stream.of("Apple", "Banana", "Tomato")  
	.collect(
		Collectors.summarizingInt(String::length)
	);  
System.out.println(stats.getCount());  
System.out.println(stats.getSum());  
System.out.println(stats.getMax());  
System.out.println(stats.getAverage());  
System.out.println(stats.getMin());
```
7. 리듀싱: `reducing()` - 값을 줄여 나가는 것
```java
List<String> names = List.of("a", "b", "c", "d");  

// 컬렉션의 리듀싱은 주로 다운 스트림에 활용  
// 모든 이름을 하나의 문자열로 이어 붙이기  
String joined1 = names.stream()  
	.collect(Collectors.reducing(  
		(s1, s2) -> s1 + " " + s2  
	)).get();  
  
String joined2 = names.stream()  
	.reduce((s1, s2) -> s1 + ", " + s2)  
	.get();  
```
7. 문자열 연결: `joining()`
```java
List<String> names = List.of("a", "b", "c", "d");  

// 문자열 전용 기능  
String joined3 = names.stream()  
	.collect(Collectors.joining(", "));  
  
String joined4 = String.join(", ", "a", "b", "c", "d");  
```