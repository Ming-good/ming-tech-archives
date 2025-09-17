## 1. 불필요 기능 제거
- **PreparedStatement 캐싱 제거**
    - 풀 내부에서 `PreparedStatement`를 캐싱하지 않음.
    - 대신 **드라이버/DB 자체 캐싱**(JDBC4 `isValid`, 서버/클라이언트 캐시)을 활용.
    - → Apache Commons DBCP는 `poolPreparedStatements`, `maxOpenPreparedStatements` 같은 옵션으로 풀 차원의 캐싱 제공.
- **커넥션 테스트 쿼리 제거/축소**
    - `validationQuery` 대신 **JDBC 표준 `Connection.isValid()`**를 활용.
    - → Commons DBCP는 기본적으로 `SELECT 1` 같은 쿼리를 주기적으로 실행.
## 2. 가볍고 빠름
- **최소 락 & 최소 오버헤드**
- **바이트코드 수준 최적화** 적용
- 철학: **“풀은 풀만 한다”** → 커넥션 풀의 본질에만 집중.
## 3. 메모리 사용량 절감
- 내부 기능을 단순화 → footprint 최소화
- (예: Statement 캐시, 광범위한 풀 내부 모니터링 제거)
## 4. 커넥션 누수 관련
- **LeakDetectionThreshold** 기능 제공
    - 커넥션을 빌린 뒤 일정 시간 내 반환하지 않으면 로그로 경고.
- “누수를 막는 것”이 아니라 → **누수를 빠르게 발견**하게 해주는 기능.
## 5. 등장 배경
- **개발자: Brett Wooldridge**
- 기존 풀의 성능 병목(락 과다, 오브젝트 생성 과다 등)을 해결하기 위해 개발.
- 모토: **“풀은 풀만 한다”**
---
## 핵심 요약
**HikariCP = 불필요 기능 제거 + 단순성 + 고성능 + 누수 감지**  
→ 현재 자바 진영에서 사실상 **표준 커넥션 풀**로 자리 잡음.