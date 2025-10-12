범위 조건 때문에 두 번째 인덱스 칼럼을 활용하지 못하는 문제는 `>`나 `<`같은 범위 대신 `IN`절을 사용함으로써 해결할 수 있는 경우가 많다.
> mysql 옵티마이저는 `IN (...)`을 하나의 큰 범위로 취급하지 않고, 여러 개의 동등 비교 조건의 묶음으로 인식하기 때문이다.
```sql
-- 문제의 쿼리
EXPLAIN 
	SELECT * 
	FROM items 
	WHERE category >= '패션' 
	AND price = 20000;
	
-- 문제 해결 쿼리
EXPLAIN 
	SELECT * 
	FROM items 
	WHERE category IN ('패션','헬스/뷰티') 
	AND price = 20000;
```

#### `IN`절 복합인덱스 처리 과정
1. 옵티마이저는 `WHERE category IN ('패션', '헬스/뷰티')` 를 `WHERE category = '패션' OR category = '헬스/뷰티'` 와 동일하게 인식한다.
2. 따라서 전체 쿼리는 내부적으로 `(category = '패션' AND price = 20000)` 또는 `(category = '헬스/뷰티' AND price = 20000)` 를 만족하는 데이터를 찾는 것으로 해석된다.
3. `idx_items_category_price` 인덱스를 사용해 `('패션', 20000)` 조합을 만족하는 데이터를 찾는다. (첫번째 동등 비교)
4. 이어서 `('헬스/뷰티', 20000)` 조합을 만족하는 데이터를 찾는다. (두 번째 동등 비교)

이렇게 작성된 `IN`쿼리는 쉽게 설명하면 아래와 같이 실행된다.
```sql
SELECT * FROM items WHERE category = '패션' AND price = 20000;
UNION ALL
SELECT * FROM items WHERE category = '헬스/뷰티' AND price = 20000;
```

