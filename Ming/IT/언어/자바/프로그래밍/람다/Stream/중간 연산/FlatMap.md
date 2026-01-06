> `flatMap`은 중첩 구조(컬렉션 안의 컬렉션, 배열 안의 배열 등)를 일차원으로 펼치는 데 사용된다.
> 예) 문자열 리스트들이 들어있는 리스트를 평탄화하면, 하나의 연속된 문자열 리스트로 만들 수 있다.
```java
List<List<Integer>> outerList = List.of(  
	List.of(1, 2),  
	List.of(3, 4),  
	List.of(5, 6)  
);  
// for  
List<Integer> forResult = new ArrayList<>();  
for (List<Integer> list : outerList) {  
	for (Integer i : list) {  
		forResult.add(i);  
	}  
}  
System.out.println("forResult = " + forResult);  
  
// map  
List<Stream<Integer>> mapResult = outerList.stream()  
	.map(list -> list.stream())  
	.toList();  
System.out.println("mapResult = " + mapResult);  
  
// flatMap  
List<Integer> flatMapResult = outerList.stream()  
	.flatMap(list -> list.stream())  
	.toList();  
System.out.println("flatMapResult = " + flatMapResult);
```
```console
forResult = [1, 2, 3, 4, 5, 6]

mapResult = [java.util.stream.ReferencePipeline$Head@21bcffb5, java.util.stream.ReferencePipeline$Head@380fb434, java.util.stream.ReferencePipeline$Head@668bc3d5]

flatMapResult = [1, 2, 3, 4, 5, 6]
```


---
## Map의 처리 과정
![[Pasted image 20250707231241.png]]
- `outerList.stream()`
	-  `List<List<Integer>>` -> `Stream<List<Integer>>`
	- `stream()` 을 호출하면 `List<List<Integer>>` 에서 밖에 있는 `List` 가 `Stream`으로 변한다.
- map(list -> list.stream())`
	- `Stream<List<Integer>>` ->  `Stream<Stream<Integer>>`
	- 내부에 있는 3개의 `List<Integer>`를 `Stream<Integer>`로 변환한다.
- `toList()`
	- `Stream<Stream<Integer>>` ->  `List<Stream<Integer>>`
	- `toList()` 는 외부 스트림을 리스트로 변환하는 기능이다.
	- 따라서 내부 요소로`Stream<Integer>` 를 3개 가지는 `List<Stream<Integer>>`로 변환된다.

## FloatMap의 처리 과정 
![[Pasted image 20250707234016.png]]
- `outerList.stream()`
	- `List<List<Integer>>`  -> `Stream<List<Integer>>`
- `flatMap(list -> list.stream())`
	- `Stream<List<Integer>>` -> `Stream<Integer>`
	- `flatMap()` 은 `Stream<Integer>` ****내부의** **값을** **꺼내서**** 외부 `Stream` 에 포함한다. 여기서는1,2,3,4,5,6의 값을 꺼낸다.
	- 이렇게 꺼낸 1,2,3,4,5,6 값 각각이 외부 `Stream` 에 포함된다. 따라서 `Stream<Integer>` 가 된다.
- `toList()`
	- `Stream<Stream<Integer>>` ->  `List<Stream<Integer>>`
	- `toList()` 는 외부 스트림을 리스트로 변환하는 기능이다.
	- 따라서 내부 요소로`Stream<Integer>` 를 3개 가지는 `List<Stream<Integer>>`로 변환된다.