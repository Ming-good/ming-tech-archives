## 내결함성의 기능
- 재시도
- 건너뛰기
>`ItemReader`는 재시도 기능의 보호 대상이 아니다. 
>`ItemReader`에서 발생한 예외는 재시도되지 않는다.
>왜냐하면 스프링 배치는 [[Mutable한 데이터소스]]를 고려했기 때문이다.
##  해결방법1, 재시도 - `RetryTemplate`
- `RetryTemplate`는 작업이 실패하면 정해진 정책에 따라 다시 시도하는 컴포넌트이다.
![[Pasted image 20251030135646.png]]
- **`canRetry()`**
	- 재시도 가능 여부 판단
	- `RetryPolicy`라는 재시도 정책을 사용해 재시도 여부를 결정한다. 
	- **디폴트는 `SimpleRetryPolicy`** 이다
- **`retryCallback()`**
	- 핵심 비즈니스 로직이 담겨있는 "정상적인 실행 + 실패 후 재시도"
- **`recoveryCallback()`**
	- **더 이상 재시도가 불가능** 할 경우 기본적으로 예외를 전파하거나 대체 로직을 수행한다.
![[609cf768-59da-4afb-b568-8f2fefffd8ed.gif]]
