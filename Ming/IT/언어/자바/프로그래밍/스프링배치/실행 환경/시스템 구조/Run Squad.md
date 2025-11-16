### 애플리케이션 진입점
`JobLauncherApplicationRunner`는 Spring Boot 애플리케이션 실행 시 자동으로 등록되어 커맨드라인 파라미터를 분석하고 처리한다.
### 파라미터 변환
- 입력한 파라미터는 `JobParametersConverter`를 통해 `JobParameters`객체로 변환된다.
- 파라미터 형식에서 값과 타입 정보를 추출하여 적절한 자바 타입으로 변환한다.
### 잡 실행 경로
- **`executeLocalJobs()`**: Spring 컨텍스트에 등록된 로컬 Job 빈을 실행
- **`executeRegisteredJobs()`**: JobRegistry에 등록된 Job을 실행 (분산 환경이나 동적 Job 생성 시 유용)
### 파라미터 가공
- 이미 존재하는 `JobInstance`인 경우, 실패/중단된 작업을 재시작할 수 있도록 파라미터 처리
- `JobParametersIncrementer`를 통해 매번 다른 `JobInstance`로 인식될 수 있도록 파라미터 증가
![[Pasted image 20251116211401.png]]