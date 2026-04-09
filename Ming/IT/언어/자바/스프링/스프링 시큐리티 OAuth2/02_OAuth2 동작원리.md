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
- `SecurityConfig` 
	- OAuth2 로그인 흐름의 진입점으로 `oauth2Login()`설정을 통해 서비스 로직과 핸들러를 방인딩하며 JWT 사용시 STATELESS설정을 적용해야한다.
- `CustomOAuth2UserService`
	- `loadUser()`메서드를 오버라이드하여 소셜 서버의 사용자 정보를 추착하고 DB저장 로직을 실행한다.
- `OAuth2AttributesRes` (커스텀 추출 데이터DTO)
	- 공급자마다 제각각인 유저 정보 응답을 공통된 규격으로 변환하는 객체
- `CustomOAuth2User`(인증 객체)
	- 시큐리티 `Authentication`객체 안에 담길 커스텀 `Principal`으로 `OAuth2User`인터페이스를 구현한다.
- `OAuth2SuccessHandler`(성공핸들러)
	- 인증이 성공한 후 호출되는 시점으로 인증된 `Principal`정보를 바탕으로 JWT를 생성.

#### JWT에서 구현해야 할 부분
- `JWTFilter`
- `JWTUtil`: JWT를 발급 및 검증하는 클래
#### 참고
- **OAuth2AuthorizationRequestRedirectFilter**
	- [OAuth2AuthorizationRequestRedirectFilter : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/web/OAuth2AuthorizationRequestRedirectFilter.html)
- ** OAuth2LoginAuthenticationFilter**
	- [OAuth2LoginAuthenticationFilter : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/web/OAuth2LoginAuthenticationFilter.html)
- * OAuth2LoginAuthenticationProvider**
	- [OAuth2LoginAuthenticationProvider : 스프링 (6.2.1) API 문서 바로가기](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/oauth2/client/authentication/OAuth2LoginAuthenticationProvider.html)
- ** OAuth2 클라이언트 세션**
	- [OAuth2 클라이언트 세션 : 개발자 유미 재생목록 바로가기](https://www.youtube.com/playlist?list=PLJkjrxxiBSFBGk0b931ZkCVlNUo7sFisu)
- **JWT**
	- [스프링 시큐리티 JWT : 개발자 유미 재생목록 바로가기](https://www.youtube.com/playlist?list=PLJkjrxxiBSFCcOjy0AAVGNtIa08VLk1EJ)