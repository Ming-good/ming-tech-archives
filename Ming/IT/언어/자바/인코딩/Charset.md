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

---
### 예제1 - Charset 별 실제 바이트 수 확인
```java
private static void encoding(String text, Charset charset) {
	byte[] bytes = text.getBytes(charset);
	System.out.printf("%s -> [%s] 인코딩 -> %s %sbyte\n", text, charset, Arrays.toString(bytes), bytes.length);
}
```

![[Pasted image 20250522235749.png]]
>  문자를 byte로 변경하려면 문자 집합이 필요하다. 또한 문자 집합을 지정하지 않으면 현재 시스템에서 사용하는 기본 문자 집합을 인코딩에서 사용한다.

---

### 예제2 - Charset  간 인코딩 호환 여부
```
== 영문 ASCII 인코딩 ==
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [US-ASCII] 디코딩 -> A
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [ISO-8859-1] 디코딩 -> A
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [EUC-KR] 디코딩 -> A
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [x-windows-949] 디코딩 -> A
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [UTF-8] 디코딩 -> A
A -> [US-ASCII] 인코딩 -> [65] 1byte -> [UTF-16BE] 디코딩 -> �
```
- ASCII는 UTF-16을 제외한 대부분의 문자 집합에 호환된다.

```
== 한글 인코딩 - 기본 ==
가 -> [US-ASCII] 인코딩 -> [63] 1byte -> [US-ASCII] 디코딩 -> ?
가 -> [ISO-8859-1] 인코딩 -> [63] 1byte -> [ISO-8859-1] 디코딩 -> ?
가 -> [EUC-KR] 인코딩 -> [-80, -95] 2byte -> [EUC-KR] 디코딩 -> 가
가 -> [x-windows-949] 인코딩 -> [-80, -95] 2byte -> [x-windows-949] 디코딩 -> 가
가 -> [UTF-8] 인코딩 -> [-22, -80, -128] 3byte -> [UTF-8] 디코딩 -> 가
가 -> [UTF-16BE] 인코딩 -> [-84, 0] 2byte -> [UTF-16BE] 디코딩 -> 가
```
- 한글 '가'는 ASCII, ISO-8859-1로 인코딩 할 수 없다.
>이 경우 흥미롭게도 숫자 63이 되는데 63은 ASCII로 `?` 라는 뜻이다. 

```
== 한글 인코딩 - 복잡한 문자 ==
뷁 -> [EUC-KR] 인코딩 -> [63] 1byte -> [EUC-KR] 디코딩 -> ?
뷁 -> [x-windows-949] 인코딩 -> [-108, -18] 2byte -> [x-windows-949] 디코딩 -> 뷁
뷁 -> [UTF-8] 인코딩 -> [-21, -73, -127] 3byte -> [UTF-8] 디코딩 -> 뷁
뷁 -> [UTF-16BE] 인코딩 -> [-67, -63] 2byte -> [UTF-16BE] 디코딩 -> 뷁
```
- MS949, UTF-8, UTF-16은 모든 한글을 표현할 수 있다.
- EUC-KR은 자주 사용하는 한글 2350개만 표현할 수 있다. 따라서 '뷁'과 문자는 문자 집합에 없으므로 인코딩할 수 없다.

```
== 한글 인코딩 - 디코딩이 다른 경우 ==
가 -> [EUC-KR] 인코딩 -> [-80, -95] 2byte -> [x-windows-949] 디코딩 -> 가
뷁 -> [x-windows-949] 인코딩 -> [-108, -18] 2byte -> [EUC-KR] 디코딩 -> ��
가 -> [EUC-KR] 인코딩 -> [-80, -95] 2byte -> [UTF-8] 디코딩 -> ��
가 -> [x-windows-949] 인코딩 -> [-80, -95] 2byte -> [UTF-8] 디코딩 -> ��
가 -> [UTF-8] 인코딩 -> [-22, -80, -128] 3byte -> [x-windows-949] 디코딩 -> 媛�
```
- 한글 인코딩시에 EUC-KR(MS949)와 UTF-8은 서로 호환되지 않는다.

```
== 영문 인코딩 - 디코딩이 다른 경우 ==
A -> [EUC-KR] 인코딩 -> [65] 1byte -> [UTF-8] 디코딩 -> A
A -> [x-windows-949] 인코딩 -> [65] 1byte -> [UTF-8] 디코딩 -> A
A -> [UTF-8] 인코딩 -> [65] 1byte -> [x-windows-949] 디코딩 -> A
A -> [UTF-8] 인코딩 -> [65] 1byte -> [UTF-16BE] 디코딩 -> �
```
- ASCII에 포함되는 영문은 UTF-16을 제외한 대부분의 문자 집합에서 호환된다.

### 한글이 깨지는 가장 큰 2가지 이유
- EUC-KR(MS949), UTF-8이 서로 호환되지 않음
	- 한글이 깨지는 대부분의 문제는 UTF-8로 인코딩한 한글을 EUC-KR(MS949)로 디코딩하거나 또는 EUC-KR(MS949)로 인코딩한 한글을 UTF-8로 디코딩할 때 발생한다.
- EUC-KR(MS949) 또는 UTF-8로 인코딩한 한글을 `ISO-8859-1`로 디코딩 할 때
	- EUC-KR(MS949) 또는 UTF-8로 인코딩한 한글을 한글을 지원하지 않는 `ISO-8859-1`로 디코딩 할 때발생한다.
