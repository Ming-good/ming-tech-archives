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

