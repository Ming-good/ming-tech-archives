## 일반 `INSERT`
```sql
-- 패킷1
INSERT INTO victims (id, name) VALUES (1, '김배치'); 
-- 패킷2
INSERT INTO victims (id, name) VALUES (2, '사불링'); 
```
-  매 쿼리마다 네트워크 패킷 발생

## `Multi-Value INSERT`
```sql
-- 패킷1
INSERT INTO victims (id, name) VALUES (1, '김배치'), (2, '사불링');
```
- 여러 값을 하나의 쿼리로 한 번에 전송
- 통합된 쿼리로 실행
- 원자성 보장

## Batch Update
 `PreparedStatement`를 재사용하여 쿼리 템플릿 하나와 여러 파라미터 세트를 함께 전송한다.
 이렇게 전달된 쿼리는 데이터베이스 서버에서 파싱되어 처리되며, 이 모든 작업은 하나의 트랜잭션 내에서 수행된다.
- 청크 단위로 묶인 수백 건의 데이터가 모두 성공하거나 모두 실패하는 원자성이 보장된다.
- 모든 데이터가 한 번의 네트워크 호출로 전송되므로 네트워크 왕복 횟수가 최소화되어 효율적인 쓰기가 가능하다.
- MySQL과 PostgreSQL에서는 설정을 통해 드라이버 레벨에서 **batchUpdate**를 Multi-Value INSERT로 자동 변환해준다.
	- Oracle에서는 기본 Multi-Value로 진행된다.
```application.yml
# MYSQL
url: jdbc:mysql://localhost:3306/mysql?rewriteBatchedStatements=true 
#POSTGRESQL
url: jdbc:postgresql://localhost:5432/postgres?reWriteBatchedInserts=true 
```

