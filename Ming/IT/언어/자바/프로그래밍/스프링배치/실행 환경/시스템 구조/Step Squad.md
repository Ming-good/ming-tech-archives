## **StepExecution 생명주기**
- SimpleStepHandler는 매번 새로운 StepExecution을 생성한다.
- 재시작 시에도 새 StepExecution이 생성되지만, 이전 ExecutionContext를 복구한다.
- 생성된 StepExecution은 즉시 메타데이터 저장소에 저장된다.
## **BatchStatus와 ExitStatus**
- BatchStatus는 실행 상태가 변경될 때마다 즉시 메타데이터 저장소에 반영된다.
- ExitStatus는 Step 실행이 완료된 후에만 StepExecution에 설정되어 저장된다.
- 예외 발생 시 적절한 BatchStatus와 ExitStatus가 설정된다.
## **StepContribution과 ExecutionContext**
- 각 청크마다 StepContribution이 생성되어 처리된 항목 수, 읽기/처리/쓰기 카운트 등을 기록한다.
- 청크 처리 후 StepContribution의 정보가 StepExecution에 반영된다.
- 청크 처리가 실패해도 중간까지의 처리 정보는 안전하게 기록된다.
## **청크 및 롤백 처리**:
- 청크 단위로 트랜잭션이 관리된다.
- 예외 발생 시 StepExecution에 롤백 카운트가 반영되며, 실제 트랜잭션 롤백은 TransactionTemplate이 담당한다.
## **마무리 단계**:
- Step 실행이 종료되면 종료 시간이 설정된다.
- StepExecutionListener.afterStep()을 통해 ExitStatus를 조정할 수 있다.
- 모든 ItemStream이 닫히며 리소스가 정리된다.
- 최종 상태가 메타데이터 저장소에 업데이트된다.