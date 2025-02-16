## 정적 팩토리 메서드를 사용하라

## 자원을 명시하지 말고 의존 객체 주입을 사용하라
1. 사용하는 자원에 따라 동작이 달라지는  클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.
2. 의존성 주입 패턴의 쓸만한 변형으로는 생성자에서 자원 팩터리를 넘겨주는 방식이 있다.

## 불필요한 객체 생성을 피하라
1.  리터럴 사용 권장
	- String a = new String() => String a = ""; 
2.  캐싱하라
3. 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의

## 다 쓴 객체 참조를 해제하라
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
