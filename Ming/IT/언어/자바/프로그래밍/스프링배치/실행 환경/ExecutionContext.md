비즈니스 로직 처리 중에 발생하는 커스텀 데이터를 관리하는 데이터 컨테이너이다
`ExecutionContext`를 통해 컬렉션의 마지막 처리 인덱스나 집계 중간 결과물 같은 데이터를 저장할 수 있다.
또한 스프링 배치 재시작 시 `ExecutionContext`의 데이터를 자동으로 환원하므로 중단된 지점부터 처리를 이어갈 수 있다.

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

### 특징
Spring Batch의 핵심 철학 중 하나는 배치 작업의 **재현 가능성**(Repeatability)과 **일관성**(Consistency)을 보장하는 것이다.

이를 위해 JobParameters는 불변하게 설계되었다.
- **재현 가능성**: 동일한 JobParameters로 실행한 Job은 항상 동일한 결과를 생성해야 한다. 실행 중간에 JobParameters가 변경되면 이를 보장할 수 없다.
- **추적 가능성**: 배치 작업의 실행 기록(JobInstance, JobExecution)과 JobParameters는 메타데이터 저장소에 저장된다. JobParameters가 변경 가능하다면 기록과 실제 작업의 불일치가 발생할 수 있다.

---
### Context로부터 Read / Write
```java
@Bean  
@StepScope  
public Tasklet attackTasklet(  
	@Value("#{jobExecutionContext['plan']}") Map<String, String> planMap  
) {  
	return (contribution, chunkContext) -> {  
		log.info("시스템 공격중: {}", planMap.get("targetSystem"));  
		log.info("목표: {}", planMap.get("objective"));  
		  
		contribution.getStepExecution()  
		.getJobExecution()  
		.getExecutionContext()  
		.put("result", "황금의 보물");  
		  
		log.info("탈취 성공 작업완료");  
		return RepeatStatus.FINISHED;  
	};  
}  
```
> 데이터를 쓸때는 `contribution`을 이용 읽을 때는 `chunkContext` 사용
### 리스너 ExecutionContext 사용
```java
@Slf4j  
@Component  
public class SendDataListener implements JobExecutionListener {  
	@Override  
	public void beforeJob(JobExecution jobExecution) {  
		Map<String, String> plan = generateData();  
		jobExecution.getExecutionContext().put("plan", plan);  
		log.info("침투 준비 완료", plan.get("targetSystem"));  
	}
}
```