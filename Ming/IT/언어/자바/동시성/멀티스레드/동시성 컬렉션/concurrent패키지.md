> 자바 1.5부터 지원

### 동시성 컬렉션의 종류
##### `List`
- `CopyOnWriteArrayList` `ArrayList` 의 대안
##### `Set`
- `CopyOnWriteArraySet` `HashSet` 의 대안
- `ConcurrentSkipListSet` `TreeSet` 의 대안(정렬된 순서 유지, `Comparator` 사용 가능)
##### `Map`
- `ConcurrentHashMap` : `HashMap` 의 대안
- `ConcurrentSkipListMap` : `TreeMap` 의 대안(정렬된 순서 유지, `Comparator` 사용 가능)

##### `Queue`
- `ConcurrentLinkedQueue` : 동시성 큐, 비 차단(non-blocking) 큐이다.

##### `Deque`
- `ConcurrentLinkedDeque` : 동시성 데크, 비 차단(non-blocking) 큐이다.

>참고로 `LinkedHashSet` , `LinkedHashMap` 처럼 입력 순서를 유지하는 동시에 멀티스레드 환경에서 사용할 수 있는 `Set` , `Map` 구현체는 제공하지 않는다. 필요하다면 `Collections.synchronizedXxx()` 를 사용해야 한다.

---
### `BlockingQueue`
#####  `ArrayBlockingQueue`
- 크기가 고정된 블로킹 큐
- 공정(fair) 모드를 사용할 수 있다. 공정(fair) 모드를 사용하면 성능이 저하될 수 있다.
##### `LinkedBlockingQueue`
- 크기가 무한하거나 고정된 블로킹 큐
##### `PriorityBlockingQueue`
- 우선순위가 높은 요소를 먼저 처리하는 블로킹 큐
##### `SynchronousQueue`
- 데이터를 저장하지 않는 블로킹 큐로, 생산자가 데이터를 추가하면 소비자가 그 데이터를 받을 때까지 대기한다. 생산자-소비자 간의 직접적인 핸드오프(hand-off) 메커니즘을 제공한다. 쉽게 이야기해서 중간에 큐 없이 생산자, 소비자가 직접 거래한다.
##### `DelayQueue`
- 지연된 요소를 처리하는 블로킹 큐로, 각 요소는 지정된 지연 시간이 지난 후에야 소비될 수 있다. 일
정 시간이 지난 후 작업을 처리해야 하는 스케줄링 작업에 사용된다.