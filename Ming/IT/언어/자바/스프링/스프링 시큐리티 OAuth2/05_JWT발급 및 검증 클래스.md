## JWT 구조
**- Header**
- JWT임을 명시
- 사용된 암호화 알고리즘
**- Payload**
- 정보
**- Signature**
- 암호화알고리즘((BASE64(Header))+(BASE64(Payload)) + 암호화키)
## 암호화 방식
**- 암호화 종류**
**-** 양방향
- 대칭키 - 이 프로젝트는 양방향 대칭키 방식 사용 : HS256
- 비대칭키
**-** 단방향

## 코드
#### JWT 발행 및 유효기간 확인 유틸
```kotlin
@Component  
class JWTUtil(  
    @Value("\${jwt.secret}")  
    val secret:String  
) {  
    private val secretKey: SecretKey = Keys.hmacShaKeyFor(secret.toByteArray(StandardCharset.UTF_8))  
    private val expirationTm = Duration.ofMinutes(10)  
    fun createJwt(userId:String, email:String):String = Jwts.builder()  
        .claims(  
            mapOf(  
                USER_ID to userId,  
                EMAIL to email  
            )  
        )  
        .issuedAt(Date.from(now()))  
        .expiration(Date.from(now().plus(expirationTm)))  
        .signWith(secretKey)  
        .compact()  
  
    fun getUserId(token:String):String = Jwts.parser()  
        .verifyWith(secretKey)  
        .build()  
        .parseSignedClaims(token)  
        .payload  
        .get(USER_ID, String::class.java)  
  
  
    fun getEmail(token:String):String = Jwts.parser()  
        .verifyWith(secretKey)  
        .build()  
        .parseSignedClaims(token)  
        .payload  
        .get(EMAIL, String::class.java)  
  
    fun isExpired(token:String):Boolean = Jwts.parser()  
        .verifyWith(secretKey)  
        .build()  
        .parseSignedClaims(token)  
        .payload  
        .expiration  
        .before(Date.from(now()))  
}
```

#### 요청 성공 처리 핸들러
```kotlin
@Component  
class CustomSuccessHandler(  
    private val jwtUtil: JWTUtil  
): SimpleUrlAuthenticationSuccessHandler() {  
    override fun onAuthenticationSuccess(  
        request: HttpServletRequest?,  
        response: HttpServletResponse?,  
        authentication: Authentication?  
    ) {  
        val principal = authentication?.principal as CustomOAuth2User  
  
        val userId = principal.getUserId()  
        val email = principal.getEmail()  
  
        val token = jwtUtil.createJwt(userId = userId, email = email)  
        response?.addCookie(createCookie("Authorization", token))  
        response?.sendRedirect("http://localhost:3000?login_success=true")  
    }  
  
    private fun createCookie(key:String,value:String): Cookie {  
        val cookie = Cookie(key, value)  
        cookie.maxAge = 60 * 10  
        cookie.path="/"  
        cookie.isHttpOnly = true  
        return cookie  
    }  
}
```

#### JWT 검증 필터
```kotlin
class JWTFilter(  
    private val jwtUtil: JWTUtil  
): OncePerRequestFilter() {  
    private val log = log()  
  
    override fun doFilterInternal(  
        request: HttpServletRequest,  
        response: HttpServletResponse,  
        filterChain: FilterChain  
    ) {  
        val cookies = request.cookies  
        val token = cookies.find { it.name == "Authorization" }?.value  
  
        if (token == null || !jwtUtil.isExpired(token)) {  
            filterChain.doFilter(request, response)  
            return  
        }  
  
        val userId = jwtUtil.getUserId(token)  
        val email = jwtUtil.getEmail(token)  
        val name = jwtUtil.getName(token)  
  
        // 유저 정보 객체  
        val customerAuthUser = CustomOAuth2User(UserDto(userId, email, name))  
  
        // 스프링 시큐리티 인증 토큰 생성  
        val authToken = UsernamePasswordAuthenticationToken(customerAuthUser, null, customerAuthUser.authorities)  
  
        // 세션에 사용자 등록 => 인증 완료후 세션 STATELESS상태를 위해 삭제 예정  
        SecurityContextHolder.getContext().authentication = authToken  
        filterChain.doFilter(request, response)  
    }  
  
}
```

#### 필터 등록
```kotlin
fun filtetChain(http: HttpSecurity): SecurityFilterChain{  
    return http.cors { it.configurationSource(corsConfigurationSource()) }  
        .csrf{it.disable()}  
        .formLogin { it.disable() }  
        .httpBasic { it.disable() }  
        .addFilterBefore(JWTFilter(jwtUtil), UsernamePasswordAuthenticationFilter::class.java)  
        .oauth2Login{  
            it.userInfoEndpoint{  
                it.userService(customerUserService)  
            }.successHandler(customerSuccessHandler)  
        }  
        .authorizeHttpRequests {  
            it.requestMatchers("/", "/api/v1/tag/test*")  
                .permitAll()  
                .anyRequest()  
                .authenticated()  
        }  
        .sessionManagement { it.sessionCreationPolicy(SessionCreationPolicy.STATELESS) }  
        .build()  
}
```