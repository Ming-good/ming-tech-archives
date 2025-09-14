>JobScope와 StepScop가 선언된 빈은 애플리케이션 구동 시점에는 우선 프록시 객체로만 존재한다. 그 후 Job이나 Step이 실행된 후에 프록시 객체에 접근을 시도하면 그 때 실제 빈이 생성된다.


## Scope 생명주기
![[Pasted image 20250914162028.png]]

## `@JobScope`
Job이 실행 될 때 실제 빈이 생성되고, Job이 종료될 때 제거되는 스코프이다.
#### 특징
- 지연된 빈 생성
	- `@JobScope`가 적용된 빈은 애플리케이션 구동 시점에는 프록시 객체만 생성된다. 실제 인스턴스는 Job이 실행 될 때 생성된다.
- Job 파라미터와의 연동
	- 지연된 빈 생성이 가능하다 보니 어플리케이션 실행 중에 전달되는 JobParameters를 Job 실행 시점에 생성되는 빈에 주입할 수 있다.
- 병렬 처리 지원
	- `@JobScope`가 선언된 빈은 `JobExecution`마다 별도의 빈이 생성되어서 동시성 문제를 방지할 수 있다.
		- EX) 상태값 공유

## `@StepScope`
`@StepScope`는 `@JobScope`와 유사하지만 Step 레벨에서 동작하는 스코프다. 
`@JobScope`가 **Job의 실행 범위**에서 빈을 관리한다면, `@StepScope`는 **Step의 실행 범위**에서 빈을 관리한다.
#### 특징
- `@StepScope`가 선언된 빈은 Step의 생명주기와 함께한다는 것을 의미한다. 즉, 각각의 Step 실행마다 새로운 빈이 생성되고, Step이 종료될 때 함께 제거된다. 

## JobScope와 StepScope 사용 시 주의사항

#### 1. 프록시 대상의 타입이 클래스인 경우 반드시 상속 가능한 클래스여야 한다.
```java
@Scope(value = "job", proxyMode = ScopedProxyMode.TARGET_CLASS) 
@Retention(RetentionPolicy.RUNTIME) 
@Documented 
public @interface JobScope {}
```
- Scope 대상자들은 CGLIB기반 프록시를 생성한다. CGLIB는 원본 객체를 대상으로 상속받아서 새로운 프록시 객체를 생성한다
#### 2. Step빈에는 `@StepScope` OR `@JobScope`를  사용하지 말라
- 스프링 배치 스텝에 `@StepScope`를 달면 Step 빈 생성과 스코프 활성화 시점이 맞지 않아 오류가 발생한다.
```java
@Bean 
@StepScope 
public Step systemDestructionStep( SystemInfiltrationTasklet tasklet ) { 
	return new StepBuilder("systemDestructionStep", jobRepository) 
				.tasklet(tasklet, transactionManager) 
				.build(); 
}
```

![[Pasted image 20250914171938.png]]
`@StepScope`로 빈을 만들기 위해서는 `StepExecution`라는 컨텍스트가 필요하지만 Step 실행전에는 존재하지 않음으로 해당 프록시에 접근하려고할 경우 오류가 발생하게 된다.
> `JobParameters`를 사용해야된다면, `Tasklet`을 구현하여 사용하던가 StepContext에서 꺼내어 `JobParameters`를 사용하면 된다.

## 컴파일 시점에  Step전달 방법
```java
// 1. 빈 주입 방식 

@Bean 
public Job systemTerminationJob(Step systemDestructionStep) { // Spring이 주입 
	return new JobBuilder("systemTerminationJob", jobRepository) 
			.start(systemDestructionStep) .build(); 
} 

// 2. 메서드 직접 호출 방식 
@Bean 
public Job systemTerminationJob() { 
return new JobBuilder("systemTerminationJob", jobRepository) 
			.start(systemDestructionStep(null)) // Job 파라미터 자리에 null 전달 
			.build(); 
}
```
- 스프링 배치에서는 지연 바인딩되는 특성을 이용하면 `null` 전달하더라도 Job이 실행될 때 `JobParameters`값을 주입받을 수 있다.