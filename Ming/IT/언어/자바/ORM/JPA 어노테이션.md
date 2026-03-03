## `@Table` 옵션
- name: 매핑한 테이블 이름 지정
- catalog: 데이터베이스 catalog매핑(catalog 기능이 있는 경우)
- schema: 데이터베이스 schema 매핑(schema기능이 있는 경우)
- uniqueConstraint:
	- `create`: 테이블 Drop + Create
	- `create-drop` : 테이블 Create후 프로그램 종료시 Drop
	- `update`: 테이블 수정
	- `validate`: 영속 객체와 테이블 매핑 확인
	- `none`: 사용하지 않음