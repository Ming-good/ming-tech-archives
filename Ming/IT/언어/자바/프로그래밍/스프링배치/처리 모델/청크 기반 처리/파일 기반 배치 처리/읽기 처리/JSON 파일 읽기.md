```java
.resource(new UrlResource("http://test.com/fi.json"))
```

```java
@Bean
@StepScope
public JsonItemReader<SystemDeath> systemDeathReader(
        @Value("#{jobParameters['inputFile']}") String inputFile) {
    return new JsonItemReaderBuilder<SystemDeath>()
            .name("systemDeathReader")
            .jsonObjectReader(new JacksonJsonObjectReader<>(SystemDeath.class))
            .resource(new FileSystemResource(inputFile))
            .build();
}
```

**JsonObjectReader**:
- **Jackson**: **JacksonJsonObjectReader**
- **Gson**: **GsonJsonObjectReader**