비즈니스 로직 처리 중에 발생하는 커스텀 데이터를 관리하는 데이터 컨테이너이다
`ExecutionContext`를 통해 컬렉션의 마지막 처리 인덱스나 집계 중간 결과물 같은 데이터를 저장할 수 있다.
또한 스프링 배치 재시작 시 `ExecutionContext`의 데이터를 자동으로 박원하므로 중단된 지점부터 처리를 이어갈 수 있다.

```java
@Bean 
@JobScope 
public Tasklet systemDestructionTasklet( 
	@Value("#{jobExecutionContext['previousSystemState']}") String prevState 
	) { 
	// JobExecution의 ExecutionContext에서 이전 시스템 상태를 주입받는다 
} 

@Bean 
@StepScope 
public Tasklet infiltrationTasklet( 
	@Value("#{stepExecutionContext['targetSystemStatus']}") String targetStatus 
) { 
	// StepExecution의 ExecutionContext에서 타겟 시스템 상태를 주입받는다 
}
```
- Job의 ExecutionContext는 Job에 속한 모든 컴포넌트에서 접근할 수 있지만, Step의 ExecutionContext는 오직 해당 Step에 속한 컴포넌트에서만 접근할 수 있다.
	- Step간의 데이터 독립성을 보장하기 위함