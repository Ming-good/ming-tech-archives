## CASE 문의 종류
1. 단순 `CASE`문
```sql
CASE 비교대상_컬럼_또는_표현식
	WHEN 값1 THEN 결과1
	WHEN 값2 THEN 결과2
	...
	ELSE 그_외의_경우_결과
END
```
2. 검색 `CASE`문
```sql
CASE
	WHEN 조건1 THEN 결과1
	WHEN 조건2 THEN 결과2
	...
	ELSE 그_외의_경우_결과
END
```

## 피벗 테이블 구현 패턴
![[Pasted image 20250930232153.png]]
1. **패턴 1**
	- `COUNT(CASE WHEN status = 'COMPLETED' THEN 1 END)`
2. **패턴 2**
	- `SUM(CASE WHEN status = 'COMPLETED' THEN 1 ELSE 0 END)`
