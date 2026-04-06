```properties
#registration
spring.security.oauth2.client.registration.서비스명.client-name=서비스명
spring.security.oauth2.client.registration.서비스명.client-id=서비스에서 발급 받은 아이디
spring.security.oauth2.client.registration.서비스명.client-secret=서비스에서 발급 받은 비밀번호
spring.security.oauth2.client.registration.서비스명.redirect-uri=서비스에 등록한 우리쪽 로그인 성공 URI
spring.security.oauth2.client.registration.서비스명.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.서비스명.scope=리소스 서버에서 가져올 데이터 범위

#provider
spring.security.oauth2.client.provider.서비스명.authorization-uri=서비스 로그인 창 주소
spring.security.oauth2.client.provider.서비스명.token-uri=토큰 발급 서버 주소
spring.security.oauth2.client.provider.서비스명.user-info-uri=사용자 정보 획득 주소
spring.security.oauth2.client.provider.서비스명.user-name-attribute=응답 데이터 변수
```

## registration과 provider
- registration: 외부 서비스에서 우리 서비스를 특정하기 위해 등록하는 정보이기 떄문에 필수이다.
- provider: 서비스별로 정해진 값이 존재하면 OAuth2 클라이언트 의존성이 유명한 서비스의 경우 내부적으로 데이터를 가지고있다.

