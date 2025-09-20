```java
@Bean  
public Job readJob(Step readStep) {  
return new JobBuilder("readJob", jobRepository)  
			.start(readStep)  
			.build();  
}  
  
@Bean  
public Step readStep(  
	FlatFileItemReader<CsvFileVo> csvReader,  
	WriterItem writerStep  
) {  
return new StepBuilder("readStep", jobRepository)  
			.<CsvFileVo, CsvFileVo>chunk(10, transactionManager)  
			.reader(csvReader)  
			.writer(writerStep)  
			.build();  
}  
  
@Bean  
@StepScope  
public FlatFileItemReader<CsvFileVo> csvReader(  
	@Value("#{jobParameters['inputFile']}") String inputFIle  
) {  
return new FlatFileItemReaderBuilder<CsvFileVo>()  
			.name("csvReader")  
			.resource(new FileSystemResource(inputFIle))  
			.delimited()  
			.delimiter(",")  
			.names("errorId", "errorDateTime", "severity", "processId", "errorMessage")  
			.targetType(CsvFileVo.class)  
			.linesToSkip(1)  
			.build();  
}  
  
@Bean  
public WriterItem writerItem() {  
	return new WriterItem();  
}  
  
  
public static class WriterItem implements ItemWriter<CsvFileVo> {  
	@Override  
	public void write(Chunk<? extends CsvFileVo> chunk) throws Exception {  
		for (CsvFileVo vo : chunk) {  
			log.info("Processing system: {}", vo);  
		}  
	}  
}  
  
@Data  
public static class CsvFileVo {  
	private String errorId;  
	private String errorDateTime;  
	private String severity;  
	private Integer processId;  
	private String errorMessage;  
}
```

###  `FlatFileItemReaderBuilder`의 주요 기능
- `targetType`: `BeanWrapperFieldSetMapper`에서 FieldSet을 객체로 매핑할 대상 도메인 클래스를 지정한다. 
	- 런타임에 객체 생성에 필요한 실제 클래스 정보를 제공한다. Java의 타입소거로 인해 제네릭 타입 정보는 런타임에서 사라지므로 `BeanWrapperFieldSetMapper`가 새로운 인스턴스를 생성하기 위해서 필수이다.
	- 제네릭 타입은 모든 `ItemReader`구현체에 전달되는 값으로 ItemReader가 반환하는 객체 타입을 컴파일 시점에 명확히 하기 위해 사용된다.
- `comments(String... comments)` 
	- 주석처리: 읽는 파일에 기본으로 `#`문자로 시작되는 라인을 주석으로 인식한다. 
- `strict()`
	- 파일과 데이터 검증의 강도를 설정하는 메서드로, 기본값은 `true`이다.
	- `true`: 
		- 파일 누락 시 예외를 발생시켜 배치를 중단한다.
		- `LineTokenizer`가 토큰화한 길이가 객체에 전달된 길이와 다를 경우 예외 발생
	- `false`: 
		- 파일이 존재하지 않아도 경고만 남기고 진행한다.
		- `LineTokenizer`가 토큰화한 길이가 객체에 전달된 길이와 다를 경우 토큰 수를 자동 보정한다.
- `beanMapperStrict()`
	- `BeanWrapperFieldSetMapper`의 `strict`설정을 제어하는 옵션으로 기본값은 `true`이다.
	- `true`:
		- FieldSet에 매핑 대상 객체에 존재하지 않는 필드가 있을 경우 예외를 발생시킨다.
	- `false`:
		- FieldSet에 매핑 대상 객체에 존재하지 않는 필드가 있을 경우 예외를 발생시키지 않고 넘어간다.

#### 정리
- .name() : Reader 식별자 설정
- .resource() : 처리 대상 파일 지정
- .delimited() : 구분자 기반 파일 읽기 모드 활성화
- .delimiter(",") : 쉼표로 데이터 구분
- .names() : 각 필드 식별자(SystemFailure의 프로퍼티 이름) 매핑
- .targetType() : 변환 대상 객체(SystemFailure) 지정
- .linesToSkip() : 헤더 라인 제거
- .strict(): 엄격한 규율 적용