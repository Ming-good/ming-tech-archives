![[Pasted image 20260406131349.png]]
#### Filter
- **JWTFilter**: 커스텀해서 등록
- **OAuth2AuthorizationRequestRedirectFilter**: 인증요청 받는 필터
```
/oauth2/authorization/서비스명 
/oauth2/authorization/naver 
/oauth2/authorization/google
```
- **OAuth2LoginAuthenticationFilter** : 외부 인증 서버에 설정할 redirect_uri
```
/login/oauth2/code/서비스명 
/login/oauth2/code/naver 
/login/oauth2/code/google
```

#### OAuth2 클라이언트에서 구현해야할 부분
- `OAuth2UserDetailsService`
- `Oauth2UserDetails`
- `LoginSuccessHandler`
#### JWT에서 구현해야 할 부분
- `JWTFilter`
- `JWTUtil`: JWT를 발급 및 검증하는 클래
#### 참고
- **OAuth2AuthorizationRequestRedirectFilter**
	- [OAuth2AuthorizationRequestRedirectFilter : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/web/OAuth2AuthorizationRequestRedirectFilter.html)
- ** OAuth2LoginAuthenticationFilter**
	- [OAuth2LoginAuthenticationFilter : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/web/OAuth2LoginAuthenticationFilter.html)
**- OAuth2LoginAuthenticationProvider**
	- [OAuth2LoginAuthenticationProvider : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/authentication/OAuth2LoginAuthenticationProvider.html)
- ** OAuth2 클라이언트 세션**
	- [OAuth2 클라이언트 세션 : 개발자 유미 재생목록 바로가기](https://www.youtube.com/playlist?list=PLJkjrxxiBSFBGk0b931ZkCVlNUo7sFisu)
- **JWT**
	- [스프링 시큐리티 JWT : 개발자 유미 재생목록 바로가기](https://www.youtube.com/playlist?list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ)