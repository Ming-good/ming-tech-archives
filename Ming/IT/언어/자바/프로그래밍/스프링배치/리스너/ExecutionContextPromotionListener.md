스프링 배치에서는 Step 수준의 ExecutionContext 데이터를 Job 수준의 ExecutionContext로 옮기는 과정을 **승격**(Promote)라고 부른다
### Step간에 데이터 공유
`리스너 등록`
```java
@Bean  
public Step scanningStep(
		JobRepository repository, 
		PlatformTransactionManager transactionManager
	) {  
	return new StepBuilder("scanningStep", repository)  
	.tasklet((contribution, chunkContext) -> {  
		String target = "판교 서버실";  
		
		contribution.getStepExecution()
		.getExecutionContext()
		.put("targetSystem", target);  
		
		log.info("타켓 스캔 완료: {}", target);  
		return RepeatStatus.FINISHED;  
	},transactionManager)  
	.listener(promotionListener())  
	.build();  
}

@Bean  
public ExecutionContextPromotionListener promotionListener() {  
	ExecutionContextPromotionListener lis = new ExecutionContextPromotionListener();  
	
	lis.setKeys(new String[]{"targetSystem"});  
	return lis;  
}
```
먼저 ExecutionContextPromotionListener에는 Step 수준의 ExecutionContext에서 Job 수준으로 승격할 데이터의 키 값을 지정해주어야 한다. 이는 `setKeys(`) 메서드를 통해 설정할 수 있다.

![[Pasted image 20250918222146.png]]
