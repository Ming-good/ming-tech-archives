### 1. `inline` (인라인 함수)
- **정의**: 함수 호출 시 새로운 스택 프레임을 생성하지 않고, **컴파일 단계에서 함수의 본문을 호출부로 직접 복사**해 넣는 방식임.
- **사용 목적**:
    - 고차 함수(람다를 인자로 받는 함수) 호출 시 발생하는 객체 생성 오버헤드 감소.
    - 함수 호출 오버헤드를 줄여 성능 최적화.
- **특징**:
    - 함수 본문이 너무 길면 바이트코드 크기가 커질 수 있음.
    - `reified` 키워드를 쓰기 위한 필수 전제 조건임.
### 2. `reified` (실체화)
- **정의**: 제네릭 타입 `T`를 런타임에도 **사라지지 않게 실체화**하여 클래스 정보에 접근할 수 있게 함.
- **사용 목적**:
    - 자바의 **타입 소거(Type Erasure)** 문제 해결.
    - 함수 내부에서 `T::class.java`나 `is T` 같은 타입 체크/캐스팅을 가능하게 함.
- **조건**: 반드시 `inline` 함수와 함께 사용해야 함 (컴파일러가 호출부의 실제 타입을 알고 코드를 복사해야 하기 때문).

---
```kotlin
// 선언부
inline fun <reified T> T.logger(): Log = LogFactory.getLog(T::class.java)
// 사용부
class UserService {
    private val log = logger() // 자동으로 LogFactory.getLog(UserService::class.java)로 치환됨
}


// 선언부 
inline fun <reified T> String.toObject(): T { 
	return ObjectMapper().readValue(this, T::class.java) 
} 
// 사용부
val user: UserDto = jsonString.toObject() // 명시적인 .class 전달이 필요 없음


// 선언부
inline fun <reified T> ApplicationContext.getBean(): T { 
	return this.getBean(T::class.java) 
} 
// 사용부 
val service = context.getBean<UserService>()
```
