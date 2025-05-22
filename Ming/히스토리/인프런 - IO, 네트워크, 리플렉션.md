
### 2025.05.22
- 소스코드 상에 [[Charset]] 문자셋 인코딩 실행 결과. 
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