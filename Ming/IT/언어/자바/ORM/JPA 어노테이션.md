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
#### `@SequenceGenerator()`
- Sequence 생성 전략은 DB의 Sequence 오브젝트를 이용해 기본키를 생성하는 방법이다.
- Sequence 전략을 사용하기 위해서는 `@SequenceGenerator`가 필요하며 이 어노테이션을 이용해 여러 옵션을 적용할 수 있다.
- 이 전략도 Identity 전략과 마찬가지고 데이터가 발생할 떄 생성되는 Sequence를 받아 영속 객체를 관리한다.
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
> 피해야 할 타입 : `float, Float, double` 등을 피해야 함.