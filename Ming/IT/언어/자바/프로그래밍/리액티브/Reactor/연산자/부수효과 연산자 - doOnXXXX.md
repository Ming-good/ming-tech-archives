| Operator          | 설명 |
|-------------------|------|
| **doOnSubscribe()**   | Publisher가 구독 중일 때 트리거되는 동작을 추가할 수 있다. |
| **doOnRequest()**     | Publisher가 요청을 수신할 때 트리거되는 동작을 추가할 수 있다. |
| **doOnNext()**        | Publisher가 데이터를 emit할 때 트리거되는 동작을 추가할 수 있다. |
| **doOnComplete()**    | Publisher가 성공적으로 완료되었을 때 트리거되는 동작을 추가할 수 있다. |
| **doOnError()**       | Publisher가 에러가 발생한 상태로 종료되었을 때 트리거되는 동작을 추가할 수 있다. |
| **doOnCancel()**      | Publisher가 취소되었을 때 트리거되는 동작을 추가할 수 있다. |
| **doOnTerminate()**   | Publisher가 성공적으로 완료되었을 때 또는 에러가 발생한 상태로 종료되었을 때 트리거되는 동작을 추가할 수 있다. |
| **doOnEach()**        | Publisher가 데이터를 emit할 때, 성공적으로 완료되었을 때, 에러가 발생한 상태로 종료되었을 때 트리거되는 동작을 추가할 수 있다. |
| **doOnDiscard()**     | Upstream에 있는 전체 Operator 체인의 동작 중에서 Operator에 의해 폐기되는 요소를 조건부로 정리할 수 있다. |
| **doAfterTerminate()**| Downstream을 성공적으로 완료한 직후 또는 에러가 발생하여 Publisher가 종료된 직후에 트리거되는 동작을 추가할 수 있다. |
| **doFirst()**         | Publisher가 구독되기 전에 트리거되는 동작을 추가할 수 있다. |
| **doFinally()**       | 에러를 포함해서 어떤 이유이든 간에 Publisher가 종료된 후 트리거되는 동작을 추가할 수 있다. |
