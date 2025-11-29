 동일 `Flow`패턴이 반복될 경우 `Flow`를 `Bean`으로 정의 가능하다

## `Job`에 `Flow`주입
```java
@Bean  
public Job flowJob() {  
    return new JobBuilder("flowJob", repository)  
            .incrementer(new RunIdIncrementer())  
            .start(flowJobFlow())  
            .end()  
            .build();  
}  
  
@Bean  
public Flow flowJobFlow() {  
    return new FlowBuilder<Flow>("flowJobFlow")  
            .start(flowStep())  
            .next(reviewDecider)  
            .on("APPROVED").to(flowStep2())  
            .from(reviewDecider).on("DETECTED").to(flowStep3())  
            .from(reviewDecider).on("*").to(flowStep4())  
            .build();  
}
```

## `Step`에 `Flow` 주입
```java
@Bean  
public Step flowStepStep() {  
    return new StepBuilder("flowStepStep", repository)  
            .flow(flowJobFlow())  
            .build();  
}

@Bean  
public Flow flowJobFlow() {  
    return new FlowBuilder<Flow>("flowJobFlow")  
            .start(flowStep())  
            .next(reviewDecider)  
            .on("APPROVED").to(flowStep2())  
            .from(reviewDecider).on("DETECTED").to(flowStep3())  
            .from(reviewDecider).on("*").to(flowStep4())  
            .build();  
}  
```