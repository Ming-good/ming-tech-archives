#### 정적 팩토리 메서드를 사용하라
---
#### 자원을 명시하지 말고 의존 객체 주입을 사용하라
1. 사용하는 자원에 따라 동작이 달라지는  클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.
2. 의존성 주입 패턴의 쓸만한 변형으로는 생성자에서 자원 팩터리를 넘겨주는 방식이 있다.
---
#### 불필요한 객체 생성을 피하라
1.  리터럴 사용 권장
	- String a = new String() => String a = ""; 
2.  캐싱하라
3. 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의
---
#### 다 쓴 객체 참조를 해제하라
```
// 코드 7-1 메모리 누수가 일어나는 위치는 어디인가?
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }

    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
     */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

다 쓴 객체의 참조를 NULL처리를 통해 해결하면 된다.

```
//제대로 구현한 pop 메서드
public Object pop() {
	if (size == 0) 
		throw new EmptyStackException();
	Object result = elements[--size];
	elements[size] = null //다 쓴 참조 해제
	return result;
}
```

**※객체 참조를 NULL 처리하는 일은 예외적인 경우 여야 한다.**
- 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.
- 캐시 역시 메모리 누수를 일으키는 주범이다. **(WeakHashMap()이나 WeakReference() 사용권장)**

---

## 동시성

#### 공유 중인 가변 데이터는 동기화해 사용해라
##### \[Tip\]
1. Thread.stop은 사용하지 말자 (메서드가 안전하지 않아 데이터 훼손 우려가 있음)
2. 자바 명세서 상에 long과 double, boolean는 읽고 쓰는 동작이 원자적이다. 하지만 필드를 읽을 때 항상 '**수정이 완전히 반영된**' 값을 얻는다고 하지만 한 스레드가 저장한 값이 다른 스레드에게 '**보이는가**'는 보장하지 않는다.
3. 증가 연산자(++)는 코드상 하나지만 실제로 변수 필드에 두 번 접근한다. 값을 읽고 후에 값을 저장하는 것이다.
##### \[권장\]
1. 동기화는 **읽기와 쓰기** 모두가 동기화 되지 않으면 동작을 보장하지 않는다.
2. AutomicLong를 사용해보자 (lock-free 방식)
	- volatile은 동기화의 두 효과 중 통신만 지원하지만 이 패키지는 배타적 실행까지 지원한다.

**읽기와 쓰기 동기화 예시**
```
static boolean stopRequested = false;  
// 쓰기
private static synchronized void requestStop() {  
	stopRequested = true;  
}  
// 읽기
private static synchronized boolean getStopRequested() {  
	return stopRequested;  
}  
public static void main(String[] args) throws InterruptedException {  
  
	Thread background = new Thread(() -> {  
		int i=0;  
		while (!getStopRequested()) {  
		  
		i++;  
		}  
		System.out.println(i);  
	});  
  
	background.start();  
		TimeUnit.SECONDS.sleep(1);  
		requestStop();  
	}
}
```