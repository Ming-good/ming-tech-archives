## 사용 예시
```java
@Slf4j  
public class ZombiewProcessCleanupTasklet implements Tasklet {  
	private final int prcToKill = 10;  
	private int killedPrc = 0;  
	
	@Override  
	public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {  
		killedPrc++;  
		log.info("프로세스 강제 종료... ({}/{})", killedPrc, prcToKill);  
		if (killedPrc >= prcToKill) {  
			log.info("안정화");  
			return RepeatStatus.FINISHED;  
		}  
			return RepeatStatus.CONTINUABLE;  
		}  
	}
}
```

## `RepeatStatus`
- `RepeatStatus.FINISHED`
	- Step의 처리가 성공이든 실패든 상관없이 해당 Step이 완료되었음을 의미.
	- 더이상 반복할 필요 없이 다은 단계로 넘어가며, 배치 잡은 다음 스텝으로 진행된다.
- `RepeatStatus.CONTINUBLE`
	- `Tasklet.execute()` 메서드가 추가로 더 실행 되어야 함을 Spring Batch Step에 알리는 신호
	- Step의 종료는 보류되고 `execute()` 메서드가 반복 호출된다.