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
### 2025.05.22
- 예제1) [[Charset]] 별 실제 바이트가 어떻게 나오는지 확인
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
### 2025.05.31
1.  [[DataOutputStream(fos)]]
	- DataStream 문자를 읽어오는 방식
	- DataStream의 장점 : 최적화
2. 객체를 한 번에 파일로 저장하는 방법 : [[직렬화 - ObjectXxxputStream]]
	- `Serializable` :  마커 인터페이스
	- ObjectOutputStream() 사용법
	- 직렬화의 한계
3. [[List]].of(obj) : 불변 객체
4. ObjectStream의 대안
	- [[직렬화 - XML]]
	- [[직렬화 - JSON]]
	- [[직렬화 - ProtoBuf, Avro]]
	-  **직렬화 정리**
		- 자바 객체 직렬화는 대부분 사용하지 않는다.
		- JSON이 사실상 표준이다. JSON을 먼저 고려하자.
		- 성능 최적화가 매우 중요하다면 Protobuf, Avro 같은 기술을 고려하자. (대부분 JSON만 사용해도 충분하다)
5. File, Files
	- File 기능 정리 : [[File 기능]]
	- Files 특징, 기능 : [[Files 기능]]
	- File과 Files 경로 다루기 : [[경로]]
	- [[Files로 문자  파일 읽기]]
	- [[파일 복사 최적화]]
	- 파일을 다루어야 할 일이 있다면 항상 `Files`의 기능을 먼저 찾아보자.
### 2025.06.03
1. 네트워크 기본 이론
	- [[IP]]
		- IP의 역할 과 한계
	- [[TCP]]
		- TCP/IP패킷 정보
		- IP의 한계를 해결한 TCP특징
	- [[UDP(사용자 데이터그램 프로토콜)]]
	- [[PORT]]
	- [[DNS조회 과정]]
2. 클라이언트 서버 연결
	- [[클라이언트 - 서버 소켓 연결 과정]]
### 2025.06.07
1. [[클라이언트 - 서버 소켓 연결 과정 (멀티스레드)]]
	- 문제 상황과 해결 방법
	- 클라이언트 2개 소켓 - 서버 1개 소켓 연결 과정  (1개 소켓 연결 안 됨)
	- 프로토콜 단위로 데이터 송신 과정
2.  자원정리
	- null체크 할 것
	- [[try-with-resource]]
		- jdk1.7부터 사용 가능
		- 구현 방법 `AutoClosable`
		- try-finally를 사용했을 때 자원 정리 문제점
		- try-with-resources의 장점
		- 부가예외
		- 서버 강제 종료시 try-finally 사용 이유
### 2025.06.08
1. [[셧다운 훅(Shutdown Hook)]]
	- 정상종료의 종류
	- 강제종료의 종류
	- 구현방법
	- 셧다운 훅으로 자원 정리시 `sleep(1000)`하는 이유
2. [[네트워크 연결 예외]]
	- UnknownHostException : 연결 자체 안 됨.
	- ConnectException : 해당IP로 접속까지는 성공, 포트에서 TCP 연결 실패, 방화벽 문제
	- TCP 연결 타임아웃
	- TCP 소켓 연결 타임아웃
3. [[연결 종료]]
	- 정상 종료
		- `Socket.close()` 호출시 `FIN` 패킷을 전송한다.
		- `FIN` 패킷을 받으면 마찬가지로 `FIN` 을 보내야 한다.
		-  InputStream 구현체 별 EOF 반환값
	- 강제 종료
		- `Socket.close()` 호출시 `FIN` 패킷을 전송한다.
		- `FIN` 패킷을 받고 데이터 전송(PUSH)할 경우 [[RST]] 응답을 받게 됨.
	- 정리
### 2025.06.13
1. 디자인 패턴
	- [[커맨드 패턴]]
	- [[Null Object 패턴]]
2. [[HTTP 메시지 구조]]
	- GET에서 메시지 바디는 지원되지 않는 곳이 있어서 권장되지 않음
