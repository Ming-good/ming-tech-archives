## 문제 케이스
#### Case1 : One쪽 조회
- One 조회 후 연관된 Many 접근 안함
	- 단, 한 번의 쿼리
- One 조회 후 연관된 Many 접근 함
	- One용 쿼리 한 번 + `List<CountryEntity>`를 순회하며 각각의 연관된 CityEntity들을 확인할 쿼리 N번
#### Case2 : Many쪽 조회 (주인쪽)
- Many 조회 후 연관된 One 접근 안함
	- CityEntity 쿼리 한 번 및 mappedBy용 쿼리 한 번 (총 2번)
- Many 조회 후 연관된 One 접근 함
	- CityEntity 쿼리 한 번 및 mappedBy용 쿼리 한 번 (총 2번)

## 쿼리 단일화
- JPQL `@Query`로 `JOIN FETCH`작성 : 제일 많이 사용
```kotlin
@Query("SELECT co FROM CountryEntity co " +  
                "JOIN FETCH co.cityEntities ci")  
fun findAllFetch():List<CountryEntity>
```
- QueryDSL로 `fetchJoin()` : 자주 사용함
- `@EntityGraph`
- Lazy쪽 조회시 Batch 설정을 통한 IN절

## 케이스별 도구 
- 1. 단순하게 `List<One>` 목록만 보여주고 연관된 Many 접근 안함
	- 접근 안하기 때문에 Lazy 로딩 사용해도 무방
- 2. `List<One>` 목록 및 각 연관 Many 접근 함
	- 페이지네이션이 들어감
		- `@BatchSize`를 통한 IN절 쿼리 수행
	- 다중 OneToMany 상황에서 각각의 OneToMany 데이터가 다 필요함
		- 하나만 join fetch 나머지는 @BatchSize를 통한 IN절 쿼리 수행 또는 전체 @BatchSize를 통한 IN절 쿼리 수행
	- SQL 조건으로 가져온 데이터가 다 필요한 경우 또는 `List<One>`의 size가 작고 연관 접근 케이스
		- join fetch 사용