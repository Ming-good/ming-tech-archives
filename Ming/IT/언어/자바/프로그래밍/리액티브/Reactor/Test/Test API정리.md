#### `expectXXXX()`메서드 - 중간 연산자

| 메서드                                                  | 설명                                                                      |
| ---------------------------------------------------- | ----------------------------------------------------------------------- |
| `expectSubscription()`                               | 구독이 이루어짐을 기대한다.                                                         |
| `expectNext(T t)`                                    | onNext Signal을 통해 전달되는 값이 파라미터로 전달된 값과 같음을 기대한다.                        |
| `expectComplete()`                                   | onComplete Signal이 전송되기를 기대한다.                                          |
| `expectError()`                                      | onError Signal이 전송되기를 기대한다.                                             |
| `expectNextCount(long count)`                        | 구독 시점 또는 이전(previous) expectNext()를 통해 기대값이 평가된 데이터 이후부터 emit된 수를 기대한다. |
| `expectNoEvent(Duration duration)`                   | 주어진 시간 동안 Signal 이벤트가 발생하지 않았음을 기대한다.                                   |
| `expectAccessibleContext()`                          | 구독 시점 이후에 Context가 전파되었음을 기대한다.                                         |
| `expectNextSequence(Iterable<? extends T> iterable)` | emit된 데이터들이 파라미터로 전달된 Iterable의 요소와 매치됨을 기대한다.                          |
#### `verifyXXXX()` 메서드 - 최종 연산자

| 메서드 | 설명 |
|--------|------|
| verify() | 검증을 트리거한다. |
| verifyComplete() | 검증을 트리거하고, onComplete Signal을 기대한다. |
| verifyError() | 검증을 트리거하고, onError Signal을 기대한다. |
| verifyTimeout(Duration duration) | 검증을 트리거하고, 주어진 시간이 초과되어도 Publisher가 종료되지 않음을 기대한다. |

