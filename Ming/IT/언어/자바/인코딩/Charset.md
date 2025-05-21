### 이용가능한 모든 문자 집합 조회
- `Charset.availableCharsets()` 를 사용하면 이용가능한 모든 문자 집합을 조회할 수 있다.여기에는 자바가 기본으로 제공하는 문자 집합과 OS가 제공하는 문자 집합을 포함한다.

### Charset.forName(obj)
- 대소문자 구분없이 인자로 문자 집합의 이름이나 별칭을 사용하면 된다.
- 특정 문자 집합을 지정해서 찾기 위한 용도

### StandardCharsets.UTF-8
- 자주 사용하는 문자 집합은 상수로 정의되어 있다.

### Charset.defaultCharset()
- 현재 시스템에서 사용하는 기본 문자 집합을 반환
> 인코딩 지정없이 사용가능한 경우는 보통 defaultCharset의 기본 문자 집합을 사용한다.