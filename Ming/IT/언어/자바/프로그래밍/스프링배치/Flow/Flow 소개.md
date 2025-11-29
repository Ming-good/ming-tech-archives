## `Flow` 란?
- `Job` 안에서 각 `Step`들이 언제 어떤 조건으로 실행될지 직접 설계하고 지휘하는 핵심 컨트롤 타워
- 특정 `Step`의 실행 결과(`ExxitStatus`)와 우리가 설정한 조건에 따라 다음에 어떤 `Step`으로 갈지 결정하는 `Job`의 두뇌 역할

## `Flow` 3대 핵심 요소
#### 상태
- `Flow` 내에서 현재 실행이 머무르거나 도달할 수 있는 모든 논리적 지점
#### 전이 조건
- 분기 기준으로 `ExitStatus`가 다음 전이를 결정하는 핵심 조건이다.
#### 전이 규칙
- `ExitCode`조건에 따라 다음 상태로의 이동을 정의한다.
![[Pasted image 20251129170914.png]]
## `Flow` 예시
```java
@Bean  
public Job flowJob(Step flowStep) {  
    return new JobBuilder("flowJob", repository)  
            .incrementer(new RunIdIncrementer())  
            .start(flowStep())  
            .on("APPROVED").to(flowStep2()).on("DETECTED").end()  
            .from(flowStep()).on("*").to(flowStep3())  
            .end()  
            .build();  
}

@Bean  
@StepScope    
public Tasklet flowTasklet() {  
	return (contribution, chunkContext) -> {  
	    contribution.setExitStatus(new ExitStatus("APPROVED", "승인 승인 승인"));  
		log.info("작업 준비...");  
		return RepeatStatus.FINISHED;  
	};  
}
```
#### 핵심 키워드 역할
- **`.on("문자열")`**: **"만약 결과가 ~라면"** (조건문)
    - 바로 앞 단계의 **`ExitStatus`**가 괄호 안의 문자열과 일치하는지 확인합니다.
- **`.to(스텝)`**: **"~로 이동해라"** (실행)
    - 앞의 `on` 조건이 맞을 경우 실행할 다음 단계를 지정합니다.
- **`.from(스텝)`**: **"다시 ~의 결과를 기준으로"** (기준점 복귀)
    - 이미 흐름이 다른 곳(`flowStep2`)으로 넘어갔지만, **다시 원래 스텝(`flowStep`)의 결과에 대한 추가 조건**을 걸고 싶을 때 사용합니다. 일종의 '갈림길로 되돌아가기'입니다.
- **`.on("*")`**: **"나머지 모든 경우"** (else)
    - 위에서 명시한 조건(예: APPROVED)을 제외한 모든 결과(FAILED, COMPLETED 등)를 잡습니다.
    - 와일드카드
	    - `*` : 어떤 값이든 처리
	    - `?` : 이 위치에 어떤 한 글자가 온다.
    - 와일드카드 전이 우선순위
	    -  `ERROR_1 > ERROR_? > ??? > ERROR_* > *`
