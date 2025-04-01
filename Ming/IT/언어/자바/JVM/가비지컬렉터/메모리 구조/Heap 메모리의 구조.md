JVM 가비지 컬렉션 알고리즘은 Tracing Garbage Collection을 사용하며, 이는 약한 세대 가설을 활용합니다.

#### [[Young Generation]]
1. 새롭게 생성된 객체가 할당되는 영역
2. 대부분의 객체가 Unreachable 상태가되어 생성되었다가 사라진다
3. young 영역에 대한 가비지 컬렉션을 Minor GC라고 부른다. 
#### Old Generation
1. Young영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
2. Young영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.
3. Old영역에 대한 가비지 컬렉션을 Major GC라고 부른다

