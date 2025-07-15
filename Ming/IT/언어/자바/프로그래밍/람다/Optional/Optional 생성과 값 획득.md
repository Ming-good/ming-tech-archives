## Optional 생성
- `Optional.of(T value)`
	- 내부 값이 확실히 `null` 이 아닐 때 사용. `null` 을 전달하면 `NullPointerException` 발생
- `Optional.ofNullable(T value)`
	- 값이 `null` 일 수도 있고 아닐 수도 있을 때 사용. `null` 이면 `Optional.empty()` 를 반환한다.
- `Optional.empty()`
	- 명시적으로 "값이 없음"을 표현할 때 사용
## Optional 값 획득
1. `isPresent()` , `isEmpty()`
	- 값이 있으면 `true`
	- 값이 없으면 `false` 를 반환. 간단 확인용.
	- `isEmpty()` : 자바 11 이상에서 사용 가능, 값이 비어있으면 `true` , 값이 있으면 `false` 를 반환
2. `get()`
	- 값이 있는 경우 그 값을 반환
	- 값이 없으면 `NoSuchElementException` 발생.
	- 직접 사용 시 주의해야 하며, 가급적이면 `orElse` , `orElseXxx` 계열 메서드를 사용하는 것이 안전.
3. `orElse(T other)`
	- 값이 있으면 그 값을 반환
	- 값이 없으면 `other` 를 반환.
4. `orElseGet(Supplier<? extends T> supplier)`
	- 값이 있으면 그 값을 반환
	- 값이 없으면 `supplier` 호출하여 생성된 값을 반환.
5. `orElseThrow(...)`
	- 값이 있으면 그 값을 반환
	- 값이 없으면 지정한 예외를 던짐.
6. `or(Supplier<? extends Optional<? extends T>> supplier)`
	- 값이 있으면 해당 값의 `Optional` 을 그대로 반환
	- 값이 없으면 `supplier` 가 제공하는 다른 `Optional` 반환
	- 값 대신 `Optional` 을 반환한다는 특징

## 정리
- `optValue.isPresent()` 가 `true` 인 이유는 `Optional.of("Hello")`로 생성했기 때문이다.
- `optEmpty.isPresent()` 가 `false` 인 이유는 `Optional.empty()` 로 생성했기 때문이다.
-  `isEmpty()` 는 자바 11부터 사용할 수 있는 메서드로, 값이 없으면 `true` 를 반환한다.
- `get()` 메서드는 `Optional` 사용 시 가능하면 피해야 한다.
	- 값이 없는 상태(`Optional.empty()` )에서 `get()` 을 호출하면 바로 예외가 터지므로, 안전하게 사용하려면 `isPresent()` 같은 사전 체크가 필요하다.
	- `get()` 보다는 `orElse()` , `orElseGet()` , `orElseThrow()` 등의 메서드를 활용하면 좀 더 세련되고 안전하게 값을 처리할 수 있다.

---
## Optional 값 획득 사용 예시
```java
// 예제: 문자열 "Hello"가 있는 Optional과 비어있는 Optional 준비  
Optional<String> optValue = Optional.of("Hello");  
Optional<String> optEmpty = Optional.empty();  
  
// isPresent(): 값이 있으면 true  
System.out.println("=== 1. isPresent() / isEmpty() ===");  
System.out.println("optValue.isPresent() = " + optValue.isPresent());  
System.out.println("optValue.isPresent() = " + optEmpty.isPresent());  
System.out.println("optValue.isEmpty() = " + optEmpty.isEmpty());  
  
// get():직접 내부의 값을 꺼냄, 값이 없으면 예외 NoSuchElementException 발생  
System.out.println("=== 2. get() ===");  
String getValue = optValue.get();  
System.out.println("getValue = " + getValue);  
// String o = optEmpty.get(); // 예외 발생  
  
// 값이 있으면 그 값, 없으면 지정된 기본값 사용  
System.out.println("=== 3. orElse() ===");  
String value1 = optValue.orElse("기본값");  
String empty1 = optEmpty.orElse("기본값");  
System.out.println("value1 = " + value1);  
System.out.println("empty1 = " + empty1);  
  
// 값이 없을 때만 람다(Supplier)가 실행되어 기본값 생성  
System.out.println("=== 4. orElseGet() ===");  
String value2 = optValue.orElseGet(() -> {  
	System.out.println("람다 호출 - optValue");  
	return "new Value";  
});  
String empty2 = optEmpty.orElseGet(() -> {  
	System.out.println("람다 호출 - optEmpty");  
	return "new Value";  
});  
System.out.println("value2 = " + value2);  
System.out.println("empty2 = " + empty2);  
  
// 값이 있으면 반환, 없으면 예외 발생  
System.out.println("=== 5. orElseThrow() ===");  
String value3 = optValue.orElseThrow(() -> new RuntimeException("값이 없습니다"));  
System.out.println("value3 = " + value3);  
  
try {  
	String empty3 = optEmpty.orElseThrow(() -> new RuntimeException("값이 없습니다"));  
} catch (RuntimeException e) {  
	System.out.println("에외 발생 : " + e.getMessage());  
}  
  
// Optional을 반환  
System.out.println("=== 6. or() ===");  
Optional<String> result1 = optValue.or(() -> Optional.of("Fallback"));  
System.out.println(result1);  
  
Optional<String> result2 = optEmpty.or(() -> Optional.of("Fallback"));  
System.out.println(result2);
```

```console
=== 1. isPresent() / isEmpty() ===
optValue.isPresent() = true
optValue.isPresent() = false
optValue.isEmpty() = true

=== 2. get() ===
getValue = Hello

=== 3. orElse() ===
value1 = Hello
empty1 = 기본값

=== 4. orElseGet() ===
람다 호출 - optEmpty
value2 = Hello
empty2 = new Value

=== 5. orElseThrow() ===
value3 = Hello
에외 발생 : 값이 없습니다

=== 6. or() ===
Optional[Hello]
Optional[Fallback]
```