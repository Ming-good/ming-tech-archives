## 처리방식
#### 1. `null`반환을 통한 데이터 필터링
- `ItemProcessor.process()`는 `null`을 반환하면 해당 item은 `ItemWriter`로 전달되는 Chunk에서 완전히 제외된다.
#### 2. 데이터 검증을 통한 실패 처리 - `ValidatingItemProcessor`
- `null`을 통한 필터링 특성을 활용하여 제공되는 구현체이다.
- `Validator<T>`
	- `setFilter(true)`: `ValidationException`이 발생했을 때 해당 데이터가 필터링되도록 `null`을 반환한다.
	- `setFilter(false)` : 기본값으로 `ValidationException`발생시 예외가 상위로 전달되어 Step이 실패하게 된다.
```java
public class CommandValidator implements Validator<Command> {
    @Override
    public void validate(Command command) throws ValidationException {
        if (command.getCommandText().contains("rm -rf /") || 
            command.getCommandText().contains("kill -9")) {
            throw new ValidationException(
                "☠️ " + command.getUserId() + 
                "의 " + command.getCommandText() + 
                " → 시스템 파괴 명령어 감지. 처단.");
        }
        
        if (command.isSudoUsed() && 
            command.getTargetProcess().contains("system")) {
            throw new ValidationException(
                "☠️ " + command.getUserId() + 
                "의 sudo " + command.getCommandText() + 
                " → 권한 남용 감지. 처단.");
        }
    }
}

@Bean
public ItemProcessor<Command, Command> commandProcessor() {
    ValidatingItemProcessor<Command> processor = 
        new ValidatingItemProcessor<>(new CommandValidator());
    processor.setFilter(true);  // ValidationException 발생 시 필터링 수행
    return processor;
}
```
#### 3. 데이터 변환
#### 4. 데이터 보강
---
## 외부 시스템 통신 최적화
#### 문제상황
- 처리해야 할 데이터가 100만 건이고 `ItemProcessor`에서 각각의 데이터마다 API를 호출해야 한다면 응답 시간이 지연되어 성능이 떨어진다.
![[Pasted image 20251026214138.png]]
#### 해결방법 - `ItemWriteListener` 
[[리스너의 종류와 특징]]
```java
default void beforeWrite(Chunk<? extends S> items) { }
```

```java
@Slf4j
@RequiredArgsConstructor
public class SystemLogEnrichListener implements ItemWriteListener<SystemLog> {
    private final ObservabilityApiClient observabilityApiClient;
    
    @Override
    public void beforeWrite(Chunk<? extends SystemLog> items) {
        List<Long> userIds = items.getItems().stream()
            .map(SystemLog::getUserId)
            .toList();
            
        // 벌크 API 호출: 청크 단위로 서버 정보를 한 번에 조회 💀
        Map<Long, ServerInfo> serverInfoMap = observabilityApiClient.fetchServerInfos(userIds);
        
        // 조회된 정보로 각 SystemLog 보강 💀
        items.getItems().forEach(systemLog -> {
            ServerInfo serverInfo = serverInfoMap.get(systemLog.getUserId());
            if (serverInfo != null) {
                systemLog.setServerName(serverInfo.getHostName());
                systemLog.setProcessName(serverInfo.getCurrentProcess());
                systemLog.setRiskLevel(calculateRiskLevel(serverInfo, systemLog.getRawCommand()));
            }
        });
        
        log.info("💀 청크 내 {}건의 SystemLog 데이터 보강 완료", items.size());
    }
}
```

---
## `CompositeItemProcessor`
여러 위임 대상 `ItemProcessor`를 순차적으로 실행하는 `ItemProcessor`의 구현체이다.
![[Pasted image 20251026220458.png]]
```java
// CompositeItemProcessor 사용
public CompositeItemProcessor(ItemProcessor<?, ?>... delegates) {
    this(Arrays.asList(delegates));
}

// CompositeItemProcessorBuilder 사용
public CompositeItemProcessorBuilder<I, O> delegates(List<? extends ItemProcessor<?, ?>> delegates) {
    this.delegates = delegates;

    return this;
}
```

## `ClassifierCompositeItemProcessor`
`ClassifierCompositeItemWriter` 처럼 Spring의 Classifier를 사용해 아이템을 처리할 `ItemProcessor`를 라우팅 한다.
```java
// ClassifierCompositeItemProcessor 사용
public void setClassifier(Classifier<? super I, ItemProcessor<?, ? extends O>> classifier) {
    this.classifier = classifier;
}

// ClassifierCompositeItemProcessorBuilder 사용
public ClassifierCompositeItemProcessorBuilder<I, O> classifier(
        Classifier<? super I, ItemProcessor<?, ? extends O>> classifier) {
    this.classifier = classifier;
    return this;
}
```
