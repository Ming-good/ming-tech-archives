```java
// 클라이언트 코드
Command command = commandMap.getOrDefault(key, defaultCommand);
```
- 이와 같이 `null` 을 객체(Object)처럼 처리하는 방법을 Null Object Pattern 이라 한다.
- 이 디자인 패턴은 `null` 대신 사용할 수 있는 특별한 객체를 만들어, `null` 로 인해 발생할 수 있는 예외 상황을 방지하고 코드의 간결성을 높이는 데 목적이 있다.
- Null Object Pattern을 사용하면 `null` 값 대신 특정 동작을 하는 객체를 반환하게 되어, 클라이언트 코드에서 `null` 체크를 할 필요가 없어진다. 
- 이 패턴은 코드에서 불필요한 조건문을 줄이고 객체의 기본 동작을 정의하는 데 유용하다.