```java
@Bean
@StepScope
public JsonFileItemWriter<DeathNote> deathNoteJsonWriter(
        @Value("#{jobParameters['outputDir']}") String outputDir) {
    return new JsonFileItemWriterBuilder<DeathNote>()
            .jsonObjectMarshaller(new JacksonJsonObjectMarshaller<>())
            .resource(new FileSystemResource(outputDir + "/death_notes.json"))
            .name("logEntryJsonWriter")
            .build();
}
```
- `jsonObjectMarshaller()` 메서드를 호출해 DeathNote 객체를 JSON으로 변환하도록 한다. 예제에서는 JacksonJsonObjectMarshaller를 사용했다.
- `resource()` 메서드에는 WritableResource 구현체인 FileSystemResource를 전달한다. 지정한 경로에 JSON 파일이 생성된다.