### 구현 방법
> try-with-resource를 이용하기 위해서는 `AutoCloseable`을 구현해야 한다.
> jdk1.7 부터 이용 가능하다.
```java
public class ResourceV2 implements AutoCloseable{  
	private String name;  
  
	public ResourceV2(String name) {  
		this.name = name;  
	}  
	  
	public void call() {  
		System.out.println(name + " call");  
	}  
	  
	public void callEx() throws CallException {  
		System.out.println(name + " callEx");  
		throw new CallException(name + " ex");  
	}  
  
	@Override  
	public void close() throws CloseException {  
		System.out.println(name + " close");  
		throw new CloseException(name + " closeEx");  
	}  
}
```

### try-finally 자원정리의 문제점
1. `cloase()` 시점에 실수로 예외를 던지면, 이후 다른 자원을 닫을 수 없는 문제 발생
2. `finally` 블럭 안에서 자원을 닫을 떄 예외가 발생하면, 핵심 예외가 `finally`에서 발생한 부가 예외로 바뀌어 버린다. 그리고 핵심 예외가  사라진다.

### try-with-resource 장점
- **리소스 누수 방지** : 모든 리소스가 제대로 닫히도록 보장한다. 실수로 `finally` 블록을 적지 않거나, `finally`블럭 안에서 자원 해제 코드를 누락하는 문제들을 예방할 수 있다.
- **코드 간결성 및 가독성 향상** : 명시적인 `close()` 호출이 필요 없어 코드가 더 간결하고 읽기 쉬워진다.
- **스코프 범위 한정** : 예를 들어 리소스로 사용되는 `resource1,2` 변수의 스코프가 `try` 블럭 안으로 한정된다. 따라서 코드 유지보수가 더 쉬워진다.
- **조금 더 빠른 자원 해제** :  기존에는 try -> catch -> finally로 catch 이후에 자원을 반납했다. Try with resource 구분은 try블럭이 끝나면 즉시 `close()`를 호출한다.
- **자원 정리 순서** : 먼저 선언한 자원을 나중에 정리한다.  
- **부가 예외 포함** : 
	- `Suppressed`로 담아서 반환.
	- 개발자는 자원 정리 중에 발생한 부가 예외를 `e.getSuppressed()`를 통해 활용 할 수 있다.
	- try-with-resources를 사용하면 핵심 예외를 반환하면서, 동시에 부가 예외도 필요하면 확인할 수 있다.
	- 자바는 e.addSuppressed(ex)라는 메서드가 있어서 예외 안에 참고할 예외를 담아둘 수 있다. 이 기능은 try-with-resources와 함께 등장했다.