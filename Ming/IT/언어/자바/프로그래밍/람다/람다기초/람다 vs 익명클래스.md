## 상속 관계
- **익명 클래스**: 일반 클래스처럼 인터페이스와 클래스를 구현하거나 상속할 수 있다.
- **람다 표현식**: 메서드를 하나만 가지는 **함수형 인터페이스**만 구현할 수 있다
	- 클라스를 상속할 수 없다. 오직 함수형 인터페이스만 구현할 수 있으며 상태나 추가적인 메서드 오버라이딩은 불가능하다
	- 람다는 단순히 함수를 정의하는 것으로, 상태나 추가적인 상속 관계를 필요로 하지 않는 상황에서만 사용할 수 있다.
## this키워드 의미
- **익명 클래스**: 내부에서 `this` 는 익명 클래스 자신을 가리킨다. 외부 클래스와 별도의 컨텍스트를 가진다.
- **람다 표현식**: `this` 는 람다를 선언한 클래스의 인스턴스를 가리킨다. 즉, 람다 표현식은 별도의 컨텍스트를가지는 것이 아니라, 람다를 선언한 클래스의 컨텍스트를 유지한다. 
	- 쉽게 말해, 람다 내부의 `this`는 **람다가 선언된 외부 클래스**의 `this`와 동일하다
```java
package lambda.lambda6;  
  
public class OuterMain {  
	private String msg = "외부 클래스";  
	public void execute() {  
		Runnable annoymous = new Runnable() {  
		private String msg = "익명 클래스";  
			@Override  
			public void run() {  
			System.out.println("[익명 클래스] this: " + this);  
			System.out.println("[익명 클래스] this.class: " + this.getClass());  
			System.out.println("[익명 클래스] this.msg: " + this.msg);  
			}  
		};  
	  
		// 2. 람도 예시  
		Runnable lambda = () -> {  
			// 람다에서의 this는 람다가 선언된 클래스의 인스턴스(외부 클래스)를 가리킴  
			System.out.println("[람다] this: " + this);  
			System.out.println("[람다] this.class: " + this.getClass());  
			System.out.println("[람다] this.msg: " + this.msg);  
		};  
		annoymous.run();  
		System.out.println("============================");  
		lambda.run();  
	}  
  
	public static void main(String[] args) {  
		OuterMain outerMain = new OuterMain();  
		System.out.println("[외부 클래스]: " + outerMain);  
		outerMain.execute();  
	}  
}
```
```console
[외부 클래스]: lambda.lambda6.OuterMain@96532d6
[익명 클래스] this: lambda.lambda6.OuterMain$1@6442b0a6
[익명 클래스] this.class: class lambda.lambda6.OuterMain$1
[익명 클래스] this.msg: 익명 클래스
============================
[람다] this: lambda.lambda6.OuterMain@96532d6
[람다] this.class: class lambda.lambda6.OuterMain
[람다] this.msg: 외부 클래스
```

## 캡처링
- **익명 클래스**
	- 익명 클래스는 외부 변수에 접근할 수 있지만, 지역 변수는 반드시 `final` 혹은 **사실상 final**인 변수만 캡처할 수 있다.
- **람다 표현식**
	- 람다도 익명 클래스와 같이 캡처링을 지원한다. 지역 변수는 반드시 `final` 혹은 **사실상 final**인 변수만 캡처할 수 있다.
 > **용어 - 사실상 final**
	 영어로 effectively final이라 한다. 사실상 `final` 지역 변수는 지역 변수에 `final` 키워드를 사용하지는 않았지만, **값을 변경하지 않는 지역 변수**를 뜻한다. `final` 키워드를 넣지 않았을 뿐이지, 실제로는 `final` 키워드를 넣은 것 처럼 중간에 값을 변경하지 않은 지역 변수이다. 따라서 사실상 `final` 지역 변수는 `final` 키워드를 넣어도 동일하게 작동해야 한다.

## 익명 클래스와 람다의 용도 구분
- **익명 클래스**
	- **상태를 유지**하거나 다중 메서드를 구현할 필요가 있는 경우
	- 기존 클래스 또는 인터페이스를 상속하거나 구현할 때
	- 복잡한 인터페이스 구현이 필요할 떄
- **람다**
	- **상태를 유지할 필요가 없고**, 간결함이 중요한 경우
	- 단일 메서드만 필요한 간단한 함수형 인터페이스 구현시
	- 더 나은 성능(미미함)과 간결한 코드가 필요한 경우