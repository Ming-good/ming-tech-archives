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