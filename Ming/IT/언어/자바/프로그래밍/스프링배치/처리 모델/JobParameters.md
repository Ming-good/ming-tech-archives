## 특징
1. 입력값 동적 변경: 실행중인 앱에 동적으로 제어 변수를 주입할 수 있다.
2. 메타데이터 기능: Job과 Step의 시작/종료 시간, 실행 상태, 처리된 레코드 수 등
	1. 스프링 배치는 `JobParameters`의 모든 값을 메타데이터 저장소에 기록한다.
	2. Job 인스턴스 식별 및 재시작 처리
	3. Job 실행 이력 추적

## JobParameter 타입 정리
Spring Batch에서 커맨드라인으로 전달된 `JobParameter`는 **DefaultJobParametersConverter**를 통해 적절한 타입으로 변환된다. 이 과정에서 내부적으로 Spring의 **DefaultConversionService**를 활용하기 때문에 다음과 같은 특징을 가진다.
#### 지원되는 타입
1. **스칼라 타입**
    - `Integer`, `Long`, `Double`, `Boolean` 등 기본적인 타입 변환 가능
2. **시간 관련 타입**
    - `LocalDate`, `LocalDateTime` 등 날짜/시간 타입 변환을 자체적으로 지원
3. **DefaultConversionService 제공 타입**
    - 문자열 기반으로 변환할 수 있는 다양한 타입 지원 (예: Enum, BigDecimal 등)
> 즉, 대부분의 일반적인 Use Case에서 사용하는 타입은 변환 가능하다.
#### 버전별 차이
- **Spring Batch 5 이전**: 지원 타입이 제한적
    - `String`, `Long`, `Double`, `Date` 단 4가지 타입만 지원
- **Spring Batch 5 이후**:
    - `DefaultConversionService`와의 통합으로 다양한 타입 변환 지원
    - 실무에서 필요한 대부분의 파라미터 타입 사용 가능

## 사용 예시
#### 명령어 표기법
```java
./gradlew bootRun --args='--spring.batch.job.name=dataProcessingJob inputFilePath=/data/input/users.csv,java.lang.String'

/* JobPrameters 기본 표기법 */
parameterName=parameterValue,parameterType,identificationFlag

===

./gradlew run --args="com.system.batch.DataProcessingJobConfig dataProcessingJob inputFilePath=/data/input/users.csv,java.lang.String userCount=5,java.lang.Integer,false"

/* CommandLineJobRunner 표기법 */
jobPath <options> jobIdentifier (jobParameters)*
```
- Job Parameter: `inputFilePath=/data/input/users.csv,java.lang.String`이다
- 구성: 이름, 값, 파라미터
	- parameterName: 배치 Job에서 파라미터 찾을 때 사용하는 key
	- parameterValue: 파라미터 실제 값
	- parameterType: 파라미터 타입을 명시하지 않을 경우 Spring Batch는 `String`타입으로 인식한다.
	- identificationFlag: Spring Batch에게 해당 파라미터가 JobInstance 식별(identification)에 사용될 파라미터인지 여부를 전달하는 값으로 `true`이면 식별에 사용된다는 의미이다. 이 플래그는 생략 가능하며 생략할 경우 `true`로 설정된다

#### 기본 JobParameter 호출
```java
@Bean  
public Job processTerminatorJob(JobRepository repository, Step terminationStep) {  
	return new JobBuilder("processTerminatorJob", repository)  
		.start(terminationStep)  
		.build();  
}  
  
@Bean  
public Step terminationStep(JobRepository repository, PlatformTransactionManager manager, Tasklet terminatorTasklet) {  
	return new StepBuilder("terminationStep", repository)  
		.tasklet(terminatorTasklet, manager)  
		.build();  
}  
  
@Bean  
@StepScope  
public Tasklet terminatorTasklet(  
	@Value("#{jobParameters['terminatorId']}") String terminatorId,  
	@Value("#{jobParameters['targetCount']}") Integer targetCount  
) {  
	return (contribution, chunkContext) -> {  
		log.info("시스템 종결자 정보:");  
		log.info("ID: {}", terminatorId);  
		log.info("제거 대상 수 {}", targetCount);  
	  
		for (int i = 1; i <= targetCount; i++) {  
			log.info("프로세스 {} 종료 완료!", i);  
		}  
		
		log.info("임무완료 프로세스 종료");  
		return RepeatStatus.FINISHED;  
	};
}

/* 실행 명령어 */
./gradlew bootRun --args='--spring.batch.job.name=processTerminatorJob terminatorId=KILL-9,java.lang.String targetCount=5,java.lang.Integer'
```


