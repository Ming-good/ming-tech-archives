## 테이블 어노테이션
#### `@Table` 옵션
- name: 매핑한 테이블 이름 지정
- catalog: 데이터베이스 catalog매핑(catalog 기능이 있는 경우)
- schema: 데이터베이스 schema 매핑(schema기능이 있는 경우)
- uniqueConstraint:
	- `create`: 테이블 Drop + Create
	- `create-drop` : 테이블 Create후 프로그램 종료시 Drop
	- `update`: 테이블 수정
	- `validate`: 영속 객체와 테이블 매핑 확인
	- `none`: 사용하지 않음
#### `@Access`
- JPA에서 객체에 대한 접근방식은 크게 필드 접근방법과 프로퍼티 접근방법으로 구분한다.
#### `@SequenceGenerator`
- Sequence 생성 전략은 DB의 Sequence 오브젝트를 이용해 기본키를 생성하는 방법이다.
- Sequence 전략을 사용하기 위해서는 `@SequenceGenerator`가 필요하며 이 어노테이션을 이용해 여러 옵션을 적용할 수 있다.
- 이 전략도 Identity 전략과 마찬가지고 데이터가 발생할 떄 생성되는 Sequence를 받아 영속 객체를 관리한다.
- 만약에 기존에 DB에 있는 시퀀스를 사용하게된다면 id위에 `sequenceName`을 지정해주면 된다.
- 옵션: 
	- name: 
	- sequenceName: 시퀀스 이름
	- initalValue: 시퀀스 시작 값 지정(스키마 자동 생성 적용시)
	- allocationSize: 시퀀스 값을 일괄 할당하기 위한 크기(default=50)
```sql
## 1부터 시작해서 50씩 증가한다.
create sequence customer_seq start with 1 increment by 50
create table customer_tb (
	id bigint not null,
	name vachar(255)
	primary key(id)
)
```
#### 기본키 매핑
- `@Id`
- `@IdClasss`
- `@EmbeddedId`
- `@GeneratedValue`: 기본키 자동 생성을 위한 어노테이션
	- identity: 기본기 생성 처리를 데이터베이스에 위임(`persist()`호출 시점에 Insert됨)
	- sequence: 시퀀스를 이용한 기본키 생성
	- table: 별도의 기본키 테이블을 이용해 기본키 생성
	- auto: 데이터베이스에 따라 기본키 생성 전략(defalut)
- `@TableGenerator`: 기본키 생성을 위해 별도의 테이블을 생성하고 이 테이블을 이용해 기본키를 생성한다.
> 피해야 할 타입 : `float, Float, double` 등을 피해야 함.


> 시퀀스를 통해 ID를 생성하는 방법
```kotlin
@Entity  
@Table(name = "customer_tb")  
@SequenceGenerator(name = "customer_tb_seq", sequenceName = "customer_tb_seq", initialValue = 1, allocationSize = 50)  
class Customer (  
    @Id  
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "customer_tb_seq")  
    var id: Int? = null,  
    var name: String? = null,  
    var regDt: Long? = System.currentTimeMillis()
    . 
    .
    .
```

>테이블을 통해 ID를 생성하는 방법
```kotlin
@TableGenerator(  
    name="id_generator",  
    table="customer_id",  
    pkColumnName = "id_name",  
    pkColumnValue = "customer_id",  
    valueColumnName = "next_value",  
    initialValue = 0,  
    allocationSize = 1  
)
class Customer (  
    @Id  
	@GeneratedValue(
		strategy = GenerationType.TABLE, 
		generator = "id_generator"
	)  
    var id: Int? = null,  
    var name: String? = null,  
    var regDt: Long? = System.currentTimeMillis()  
    .
    .
    .
```

## 필드와 칼럼 매핑
#### `@Column`
![[Pasted image 20260309230113.png]]
![[Pasted image 20260309230053.png]]

#### `@Temporal`
영속 객체의 날짜 및 시간 필드에 적용됨
![[Pasted image 20260309230904.png]]
![[Pasted image 20260309230859.png]]
- Java8  이후 java.time 패키지의 `LocalDate`, `LocalTim`, `LocalDateTime`을 사용할 경우 적용하지 않는다.
#### `@Lob`
- RDBMS에서 대규모 데이터를 저장하기 위해 BLOB OR CLOB 데이터 유형을 지원한다.
- BLOB: 바이트 까지의 이미지, 파일, 기타 유형의 데이터를 저장
- CLOB: 기가 바이트 까지의 긴 문자열을 저장
- `@Lob`: 어노테이션이 선언된 객체의 필드 및 속성의 유형에 따라 BLOB, CLOB로 구분된다.
- 주로 `@Basic`어노테이션과 함께 LAZY 로딩을 선언해서 사용한다
![[Pasted image 20260309231420.png]]

#### `@Enumerated`
- Enum 유형으로 선언된 값은 배열과 동일하게 ordinal이라 부르는 인덱스 값에 연결된다.
- Enum 유형의 필드 또는 속성을 맵핑하는 경우 기본값으로 EnumType.ORDINAL이 적용된다.
> EnumType.ORDINAL은 사용을 권장되지 않는다. ORDINAL을 사용할 경우 위에서부터 순서대로 0,1,2,3이 되는데 새로운 값이 추가될 경우 기존 상수의 값이 밀려서 변경이 있을 수 있기 때문

![[Pasted image 20260309231913.png]]

#### `@Transient`
- `@Transient`가 적용된 필드는 영속화 되지 않는다.
- `@Transient`는 주로 실행 시점에 참조되는 필드 또는 계산되는 필드에 사용된다.
- 속성(property)기반 접근일 경우 `@Transient`는 getter에 적용된다.
- 자바에서 제공하는 `transient` 키워드와 `@Transient` 어노테이션의 사용목적은 거의 동일하다.