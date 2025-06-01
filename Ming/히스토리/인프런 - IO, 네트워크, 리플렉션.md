### 2025.05.31
1. DataStream 문자를 읽어오는 방식 : [[DataOutputStream(fos)]]
### 2025.05.28
1. 문자를 다루는 I/O클래스 : [[스트림을 문자로]]
	- 문자 -> byte[] 전환해주는 `OutputStreamWriter`
	- byte를 다루는 클래스와 문자를 다루는 클래스 UML
	- `OutputStreamWriter` 마저 간소화한  `FileWriter`
	- `Writer`, `Reader` 클래스의 Buffer 클래스 사용법
2. 기타 스트림들
	- [[PrintStream(fos) - 콘솔 출력하듯]]
	- [[DataOutputStream(fos)]] : 데이터 형식 그대로 저장
	- ![[Pasted image 20250528222133.png]]

### 2025.05.25
- 예제2) [[Charset]] 별 인코딩, 디코딩시 호환 여부
- FileInputStream과 FileOutputStream 사용법 : [[파일 입출력]]
	- 기능
	- 부분으로 나누어 읽기 VS 전체 읽기
- I/O의 기본 추상 클래스 : [[IO 기본 추상 클래스]]
	- 메모리 스트림 예시
	- 콘솔 스트림 예시
- [[추상화의 장점]]
- I/O성능 개선 : [[IO성능 개선]]
	- 일반적인 FileXxxStream 사용시 속도가 느린 이유
	- 직접 버퍼 사용
	- BufferedOutputStream 사용
	- [[기본 스트림, 보조 스트림]]
	- BufferedInputStream 사용 
	- 버퍼를 직접 다루는 것 보다 BufferedXxx성능이 떨어지는 이유
	- 한 번에 읽고 쓰기
### 2025.05.22
- 예제1) [[Charset]] 별 실제 바이트가 어떻게 나오는지 확인
### 2025.05.21
- 문자 인코딩의 역사 : [[컴퓨터와 문자 인코딩]]
	- ASCII란?
	- ISO_8859_1 : 서유럽 중심 문자 집합
	- EUC-KR : 한국에서도 사용할 수 있는 문자 집합
	- MS949 : 마이크로소프트가 만든 EUC-KR 확장 인코딩
	- 유니코드의 등장
	- UTF-16 : 다국어 표현 가능. ASCII와 호환 안 됨(2byte 고정)
	- UTF-8 : 다국어 표현 가능. ASCII와 호환 가능. 가변문자코딩
- [[Charset]]의 메서드
	- forName(obj)
	- StandardCharsets
	- defaultCharset()