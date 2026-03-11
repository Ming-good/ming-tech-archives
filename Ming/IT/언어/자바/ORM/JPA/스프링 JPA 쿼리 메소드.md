### 1. 쿼리 메소드 (Query Methods)
가장 기본이 되는 마법 같은 기능입니다. 이름만 잘 지으면 쿼리가 자동으로 생성
- **조건 조회:** `findBy` + `필드명`
    - `findByUsername(String name)`
    - `findByUsernameAndAge(String name, int age)` : AND 조건
    - `findByAgeGreaterThan(int age)` : `age > ?` (초과)
- **검색:**  `findByUsernameContaining(String name)` : `LIKE %name%`
- **결과 제한:**  `findFirst3By...` : 상위 3건만 조회
- **존재 여부/카운트:**
    - `existsByUsername(String name)` : 존재하면 true (매우 자주 씀)
    - `countByStatus(Status status)` : 개수 반환
## 2. 페이징과 정렬
```java
Page<Member> findByAge(int age, Pageable pageable);

// 0페이지에서 10개씩, 나이순으로 정렬해서 가져와라
PageRequest pageRequest = PageRequest.of(0, 10, Sort.by(Sort.Direction.DESC, "age"));
Page<Member> page = memberRepository.findByAge(20, pageRequest);

List<Member> content = page.getContent(); // 실제 데이터
long total = page.getTotalElements(); // 전체 개수
```
## 3. 벌크 업데이트
```java
@Modifying(clearAutomatically = true) // 실행 후 영속성 컨텍스트 초기화 (중요!)
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

## 요약 가이드
- **조회 조건이 2개 이하:** 메소드 이름 (`findBy...`)
- **조회 조건이 많거나 JOIN이 복잡:** `@Query`
- **검색 조건이 동적으로 바뀜:** `QueryDSL`