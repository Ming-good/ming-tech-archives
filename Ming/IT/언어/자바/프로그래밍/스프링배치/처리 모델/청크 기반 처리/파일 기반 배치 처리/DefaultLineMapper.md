## 핵심 기능
- **토큰화**(`LineTokenizer`): 하나의 문자열 라인을 토큰단위로 분리한다.
- 객체 매핑(`FieldSetMapper`): 분리된 토큰들을 도메인 객체의 프로퍼티에 매핑한다.

```java
@Override
public T mapLine(String line, int lineNumber) throws Exception {
	// 1단계: 문자열을 토큰화해 FieldSet 반환
    FieldSet fieldSet = tokenizer.tokenize(line);  
    
    // 2단계: FieldSet을 객체로 매핑	
    return fieldSetMapper.mapFieldSet(fieldSet);   
}
```

## 토큰화
> `LineTokenizer`라는 컴포넌트를 활용하여 파일에서 읽은 한 줄의 텍스트를 개별 토큰으로 분리한다.
>  이후 그 결과를 `FieldSet`객체로 만들어 반환한다.
![[Pasted image 20250920204624.png]]

#### `LineTokenizer`의 대표적인 구현체
- `DelimitedLineTokenizer`: 쉼표와 같은 구분자로 구분된 데이터를 토큰화 한다.
- `FixedLengthTokenizer`: 고정 길이로 구분된 데이터를 토큰화한다.

## 객체 매핑
>`FieldSetMapper`의 `mapFieldSet()`메서드로 `FieldSet`객체를 입력 받아 객체를 매핑한다.

```java
public interface FieldSetMapper<T> {
    T mapFieldSet(FieldSet fieldSet) throws BindException;
}
```
- 별도 설정이 없다면 `BeanWrapperFieldSetMapper`를 기본 `FiledSetMapper`로 사용된다.
