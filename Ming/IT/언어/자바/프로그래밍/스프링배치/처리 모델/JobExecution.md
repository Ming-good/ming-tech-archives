> Job의 실행과 관련된 모든정보를 담고 있는 객체로 Job 실행 시점에 생성되어 잡의 실행 상태, JobParameters, 실행결과 등을 포함한다.

`호출 예시`
```java
@Slf4j 
@Component 
public class SystemDestructionTasklet implements Tasklet { 

@Override 
public RepeatStatus execute(
	StepContribution contribution, 
	ChunkContext chunkContext) { 
		JobParameters jobParameters = chunkContext.getStepContext() 
										.getStepExecution() 
										.getJobParameters(); 
		String targetSystem = jobParameters.getString("system.target"); 
		long destructionLevel = jobParameters.getLong("system.destruction.level"); 
		log.info("타겟 시스템: {}", targetSystem); 
		log.info("파괴 레벨: {}", destructionLevel); 
		return RepeatStatus.FINISHED; 
	} 
}
```
-`jobParameters`를 `StepExecution`에서 호출하는 이유는 `JobExecution`의 부모 클래스가 `StepExecution이기 때문이다`

![[Pasted image 20250914000923.png]]
