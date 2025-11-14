![[Pasted image 20251113174913.png]]
## `JobInstance`의 생성과 저장
1. 기존 `JobInstance`존재 여부 확인
	- `JobLauncher.run()` 호출되면 `JobInstance`가 생성됐었는지 확인한다
2. 첫 실행의 경우
	- `JobInstance`가 없다면 생성하고 메타데이터 저장소에 저장한다
3. 재시작의 경우
	- 기존 `JobInstance`가 있다면 생성하지 않고 기존 것을 그대로 재사용한다.
![[Pasted image 20251113175144.png]]

## `JobExecution`의 생성과 저장
1. 생성 단계
	-  `JobInstance`생성시 첫 실행이든 재시작이든 상관없이 무조건 새로운 `JobExecution` 객체를 생성한다
2. 즉시 저장
	- 생성된 JobExecution은 바로 jobExecutionDao.saveJobExecution()을 통해 메타데이터 저장소에 기록된다. 지연 없이 즉시 말이다.
3. 실시간 상태 추적
	- 이후 Job 실행 과정에서 상태가 변경될 때마다 변경사항이 실시간으로 메타데이터 저장소에 반영된다.
		- (`STARTING` → `STARTED` → `COMPLETED` 등) 
		- `JobRepository.update(jobExecution)`을 통해 
![[Pasted image 20251113175821.png]]
## `ExecutionContext`의 생성과 저장

1. `ExecutionContext`결정
	- SimpleJobRepository.createJobExecution() 메서드에서 JobExecution이 사용할 ExecutionContext를 결정한다.
	- 첫 실행이면 새로 만들고, 재시작이면 이전 JobExecution의 ExecutionContext를 메타데이터 저장소에서 가져온다.
2. 즉시 저장
	- 결정된 `ExecutionContext`는 즉시 메타데이터 저장소에 저장된다.
3. 스텝 완료마다 업데이트
	- 각 스텝 실행이 끝날 때마다 `SimpleStepHandler`가 변경된 `ExecutionContext`를 메타데이터 저장소에 업데이트한다.
![[Pasted image 20251113180243.png]]
