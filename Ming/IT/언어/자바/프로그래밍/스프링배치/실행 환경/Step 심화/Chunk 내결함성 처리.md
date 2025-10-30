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
- **`retryCallback()`**
	- 핵심 비즈니스 로직이 담겨있는 "정상적인 실행 + 실패 후 재시도"
- **`recoveryCallback()`**
	- **더 이상 재시도가 불가능** 할 경우 기본적으로 예외를 전파하거나 대체 로직을 수행한다.
![[609cf768-59da-4afb-b568-8f2fefffd8ed.gif]]
#### 재시도 판단 정책 - `RetryPolicy`
- `RetryPolicy`라는 재시도 정책을 사용해 재시도 여부를 결정한다. **디폴트는 `SimpleRetryPolicy`**이다
#### 재시도 처리 원리
- 스프링 배치는 내결함성 기능이 활성화된 경우 `ItemReader`가 읽어들인 input Chunk를 별도로 저장해둔다.
- 재시도가 필요할 때 Chunk를 재사용하여 처리할 수 있다.
#### 재시도 설정
```java
@Bean
public Step terminationRetryStep() {
    return new StepBuilder("terminationRetryStep", jobRepository)
            .<Scream, Scream>chunk(3, transactionManager)
            .reader(terminationRetryReader())
            .processor(terminationRetryProcessor())
            .writer(terminationRetryWriter())
            .faultTolerant() // 내결함성 기능 ON
            .retry(TerminationFailedException.class)   // 재시도 대상 예외 추가
            .retryLimit(3)
            .listener(retryListener())
            .build();
}
```
- `faultTolerant()`
	- 재시도나 스킵 같은 내결함성 기능 활성화 ON
	- `SimpleRetryPolicy` 판단 기준
		- 발생한 예외가 사전에 지정된 예외 유형에 해당하는지.
		- 현재 재시도 횟수가 최대 허용 횟수를 초과하지 않았는지.
- `retry(Exception)`
	- 어떤 예외가 발생했을 때 재시도를 수행할지 결정
- `noRetry(Exception)`
	- 상위 예외 클래스의 특정 하위 예외만 재시도에서 제외하고 싶을 때 사용
- `retryLimit(n)`
	- 허용 가능한 총 시도 횟수를 지정
		- (정확히는 재시도 횟수가 아닌, 허용가능한 retryCallback 호출 회수이다.)
	- 실제 허용 가능 재시도 횟수 = `retryLimit - 1`
---

## `ItemProcessor`트랜잭션 비처리
-  `processorNonTransactional()`
	- 해당 설정을 사용하면 스프링 배치는 `ItemProcessor`를 비트랜잭션 상태로 표시하여 한 번 처리된 아이템의 결과를 캐시에 저장한다.
	- 여전히 청크 단위 트랜잭션 롤백된다.  
	- 해당 기능을 활성화할 경우
		- 성공적한 아이템는 `process()`호출시에 캐싱된 데이터를 돌려 받아 비용을 0에 가까이 소모한다.
		- 실패한 아이템에 대해서만 `process()`로직이 수행된다.
```java
@Bean
public Step terminationRetryStep() {
    return new StepBuilder("terminationRetryStep", jobRepository)
            .<Scream, Scream>chunk(3, transactionManager)
            .reader(terminationRetryReader())
            .processor(terminationRetryProcessor())
            .writer(terminationRetryWriter())
            .faultTolerant()
            .retry(TerminationFailedException.class)
            .retryLimit(3)
            .listener(retryListener())
            .processorNonTransactional() // ItemProcessor 비트랜잭션 처리
            .build();
}
```

