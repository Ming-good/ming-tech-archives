```java
@FunctionalInterface  
public interface JobParametersValidator {  
  
void validate(@Nullable JobParameters parameters) throws JobParametersInvalidException;  
}
```

```java
@Component  
public class Validator implements JobParametersValidator {  
  
	@Override  
	public void validate(JobParameters parameters) throws JobParametersInvalidException {  
		Long level = parameters.getLong("level");  
		System.out.println("LEVEL: " + level);  
	}  
}

@Bean  
public Job processTerminatorJob(JobRepository repository, Step terminationStep) {  
return new JobBuilder("processTerminatorJob", repository)  
				.validator(validator)  
				.start(terminationStep)  
				.build();  
}
```

## `DefaultJobParametersValidator`
스프링에서는 기본 Validator을 제공한다
```java
@Bean  
public Job processTerminatorJob(JobRepository repository, Step terminationStep) {  
	return new JobBuilder("processTerminatorJob", repository)  
			.validator(new DefaultJobParametersValidator(  
				new String[]{"level"},  
				new String[]{"level2"}  
			))  
			.start(terminationStep)  
			.build();  
}
```
- 첫번째 파라미터: 필수
- 두번째 파라미터: 선택