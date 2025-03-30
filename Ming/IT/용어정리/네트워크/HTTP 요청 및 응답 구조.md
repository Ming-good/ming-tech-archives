
### 요청
```
METHOD /경로 HTTP/1.1
Host: example.com
Header1: 값1
Header2: 값2

(Body - 선택사항, POST/PUT 등에서 사용)
```

```
GET /users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer abc123
Accept: application/json

```

### 응답
```
HTTP/1.1 상태코드 상태메시지
Header1: 값1
Header2: 값2

(Body - 응답 데이터)
```
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "홍길동"
}

```
