## 응답 전문
```java
private void responseToClient(PrintWriter writer) {
	// 웹 브라우저에 전달하는 내용
	String body = "<h1>Hello World</h1>";
	int length = body.getBytes(UTF_8).length;
	
	StringBuilder sb = new StringBuilder();
	sb.append("HTTP/1.1 200 OK\r\n");
	sb.append("Content-Type: text/html\r\n");
	sb.append("Content-Length: ").append(length).append("\r\n");
	sb.append("\r\n"); // header, body 구분 라인
	sb.append(body);
	
	log("HTTP 응답 정보 출력");
	System.out.println(sb);
	writer.println(sb);
	writer.flush();
}
```
- 시작라인, 헤더, HTTP메시지 바디를 전달
- HTTP 공식 스펙에서 다음 라인은 `\r\n`(캐리지 리턴 + 라인 피드)로 표현한다.
> 참고로 \n만 사용해도 대부분 웹 브라우저는 문제없이 동작한다.

## HTTP 요청 메시지

```http
GET / HTTP/1.1
Host: localhost:12345
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36(KHTML, like Gecko) Chrome/128.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: ko,en;q=0.9,en-US;q=0.8,ko-KR;q=0.7
```
#### 헤더
- `Host` : 접속하는 서버 정보
- `User-Agent` : 웹 브라우저의 정보
- `Accept` : 웹 브라우저가 전달 받을 수 있는 HTTP 응답 메시지 바디 형태
- `Accept-Encoding` : 웹 브라우저가 전달 받을 수 인코딩 형태
- `Accept-Language` : 웹 브라우저가 전달 받을 수 있는 언어 형태