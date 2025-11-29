## `ExitCode`를 직접 사용했을 때 문제점
1. `Step`의 본질적 기능 오염
	- Step 내부에 **ExitCode** 결정 로직이 증가하면서 진짜 핵심 비즈니스 로직은 뒤로 밀려나게 된다.
2. 분기 로직의 은폐
	- 청크 지향 처리에서는 **ExitCode** 설정을 위해 StepExecutionListener를 별도로 구현해야 한다. 이렇게 되면 흐름 제어 로직이 리스너에 숨어 전체 흐름을 이해하기 어려워진다.
## `JobExecutionDecider`

```java
@Component  
@Slf4j  
public class ReviewDecider implements JobExecutionDecider {  
  
    @Override  
    public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {  
        // StepExecution의 ExecutionContext에서 분석 결과 추출  
        ExecutionContext executionContext = stepExecution.getExecutionContext();  
        int reviewScore = executionContext.getInt("reviewScore");  
  
        log.info("수강생 리뷰 점수 기반 강의 분류 중", reviewScore);  
  
        // 리뷰 점수에 따른 강의 분류  
        if (reviewScore > 10) {  
            log.error("스프링 배치 마스터 감지!!!");  
            return new FlowExecutionStatus("APPROVED");  
        } else if (reviewScore >= 8) {  
            log.info("우수 강의 감지! 홍보 대상으로 분류");  
            return new FlowExecutionStatus("DETECTED");  
        } else if (reviewScore >= 5) {  
            log.info("평균 강의 감지. 일반 관리 대상으로 분류");  
            return new FlowExecutionStatus("AVERAGE_COURSE");  
        } else {  
            log.warn("저평가 강의 감지! 개선 필요 대상으로 분류");  
            return new FlowExecutionStatus("NEEDS_IMPROVEMENT");  
        }  
    }  
}

@Bean  
public Job flowJob(Step flowStep) {  
    return new JobBuilder("flowJob", repository)  
            .incrementer(new RunIdIncrementer())  
            .start(flowStep())  
            .next(reviewDecider)  
            .on("APPROVED").to(flowStep2())  
            .from(reviewDecider).on("DETECTED").to(flowStep3())  
            .from(reviewDecider).on("*").to(flowStep4())  
            .end()  
            .build();  
}

@Bean  
@StepScope  
public Tasklet flowTasklet(  
        @Value("#{jobParameters['score']}") String score  
) {  
    return (contribution, chunkContext) -> {  
        contribution
	        .getStepExecution()
	        .getExecutionContext()
	        .putInt("reviewScore", Integer.parseInt(score)); 
	         
        log.info("작업 준비...");  
        return RepeatStatus.FINISHED;  
    };  
}
```