```kotlin
@Configuration  
@EnableWebSecurity  
class SecurityConfig {  
    @Bean  
    fun filtetChain(http: HttpSecurity): SecurityFilterChain{  
        return http.cors { it.configurationSource(corsConfigurationSource()) }
	        // csrf disable  
            .csrf{it.disable()}  
            //
            .formLogin { it.disable() }  
            .httpBasic { it.disable() }  
            .oauth2Login(Customizer.withDefaults<>())  
            .authorizeHttpRequests {  
                it.requestMatchers("/")  
                    .permitAll()  
                    .anyRequest()  
                    .authenticated()  
            }  
            .sessionManagement { it.sessionCreationPolicy(SessionCreationPolicy.STATELESS) }  
            .build()  
    }  
  
    @Bean  
    fun corsConfigurationSource(): CorsConfigurationSource {  
        val conf = CorsConfiguration()  
        conf.allowedOrigins = listOf("http://localhost:3000")  
        conf.allowedMethods = listOf("GET", "POST", "PUT", "DELETE")  
        conf.allowedHeaders = listOf("*")  
        conf.allowCredentials = true  
  
        val registry = UrlBasedCorsConfigurationSource()  
        registry.registerCorsConfiguration("/**", conf)  
        return registry  
    }  
  
    @Bean  
    fun successHandler(): AuthenticationSuccessHandler {  
        val handler = SimpleUrlAuthenticationSuccessHandler()  
        val targetUri ="http://localhost:3000?login_success=true"  
        handler.setDefaultTargetUrl(targetUri)  
        return handler  
    }  
}
```