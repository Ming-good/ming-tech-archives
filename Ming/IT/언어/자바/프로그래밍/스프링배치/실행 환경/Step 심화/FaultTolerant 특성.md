## Chunk처리의 구조적 한계
#### 내결함성
>`ItemReader`는 재시도 기능의 보호 대상이 아니다. `ItemReader`에서 발생한 예외는 재시도되지 않는다.
>왜냐하면 스프링 배치는 [[Mutable한 데이터소스]]를 고려했기 때문이다.
##  해결방법1, 재시도 - `RetryTemplate`
- `retryCallback`
- `recoveryCallback`
#### 재시도 심판관 - `RetryPolicy`

#### 재시도 설정
- `faultTolerant()`
- `retry()`
- `noRetry()`
- `retryLimit()`
- 
