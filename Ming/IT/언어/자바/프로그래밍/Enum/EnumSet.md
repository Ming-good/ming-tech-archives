`EnumSet`은 **enum 타입 전용 Set 컬렉션**으로, 성능과 메모리 효율을 극대화하기 위해 특별하게 설계된 클래스

## 1. EnumSet이란?
- **`enum` 전용** Set 컬렉션 (`java.util.EnumSet`)
- 내부적으로 **비트 벡터(bit vector)** 방식으로 동작
- 일반 `Set`보다 훨씬 **빠르고 가볍다**
- `null` 요소는 허용하지 않음
## 2. 특징
| 항목    | 설명                                |
| ----- | --------------------------------- |
| 내부 구조 | `enum.ordinal()` 값을 비트 인덱스로 사용    |
| 성능    | 포함 여부 판단, 추가/삭제 모두 O(1)           |
| 메모리   | `enum` 개수가 64개 이하이면 `long` 하나로 표현 |
| 제한사항  | `enum` 타입만 가능 (일반 객체 X)           |
|       |                                   |
## 3. 생성 방법
```
EnumSet<Day> set = EnumSet.of(Day.MONDAY, Day.WEDNESDAY);     // 특정 값만
EnumSet<Day> all = EnumSet.allOf(Day.class);                   // 전체 값
EnumSet<Day> none = EnumSet.noneOf(Day.class);                 // 빈 Set
EnumSet<Day> range = EnumSet.range(Day.MONDAY, Day.FRIDAY);    // 범위 지정
```
## 4. 값 꺼내는 방법
|방법|예시 코드|설명|
|---|---|---|
|전체 순회|`for (Day d : set)`|모든 값 출력|
|하나 꺼내기|`set.iterator().next()`|첫 값 가져오기|
|스트림 처리|`set.stream().forEach(...)`|필터링 등|
|배열 변환|`Day[] arr = set.toArray(new Day[0])`|인덱스 접근 가능|
|인덱스 접근|❌ 직접 인덱스로는 못 꺼냄|배열로 변환 필요|
## 5. 사용 이유
- enum 값들을 **중복 없이 관리**할 때 (예: 권한, 상태, 요일 등)
- 일반 `HashSet<Enum>`보다 **빠르고 메모리 효율적**

