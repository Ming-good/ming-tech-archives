## `FlowExecutionStatus`
1. Step이 실행을 마치면 ExitStatus의 `exitCode` 문자열을 반환
2. 이 `exitCode` 문자열을 가지고 **FlowExecutionStatus** 객체가 생성됨
3. **Flow DSL**에서 `.on("PLAGIARISM_DETECTED")` 메서드로 지정한 문자열 패턴이 이 **FlowExecutionStatus**의 `name` 필드와 매칭되는지 확인
4. 패턴이 매칭되면 해당 `.to()` 메서드로 지정된 다음 상태로 전이

## `EndState`
모든 `Flow`는 반드시 어떤 `EndState`로 전이된다.
- EndState의 `status` 필드가 `FlowExecutionStatus("COMPLETED")`면 Job은 완료 상태로, `FlowExecutionStatus("FAILED")`면 실패 상태로, `FlowExecutionStatus("STOPPED")`면 중단 상태로 최종 결정되는 것이다.
- `EndState`전이 메서드
	- `end()` : 결과와 상관없이 항상 `COMPLETED` 상태로 전이
	- `stop()` : 결과와 상관없이 항상 `STOPPED` 상태로 전이
	- `fail()` : `FAILED` 상태의 EndState로 전이
```java
.from(analyzeLectureStep) .on("TOO_EXPENSIVE").to(priceGougerPunishmentStep).on("*").end()  

.from(analyzeLectureStep) .on("666_UNKNOWN_PANIC").to(adminManualCheckStep).on("*").stop()

.from(analyzeLectureStep) 
.on("PLAGIARISM_DETECTED").fail()
```
#### 커스텀 `EndState`
- 동작 방식 : 
	- `FlowExecutionStatus`(흐름 상태)의 이름 앞부분(Prefix)이 표준 `BatchStatus`와 일치하는지 확인하여 해당 배치 상태로 변환하는 로직
	- (예: `FAILED_CUSTOM_ERROR`라는 상태가 들어오면, `FAILED`로 시작하므로 `BatchStatus.FAILED`를 반환)
```java
.from(analyzeLectureStep) 
.on("APPROVED").to(approveImmediatelyStep).on("*").end("COMPLETED_BY_SYSTEM")
```