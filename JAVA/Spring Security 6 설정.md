```java
@Configuration  
@EnableWebSecurity  
public class SecurityConfig {  
  
    @Autowired  
    AdminAuthenticatorProvider adminAuthenticatorProvider;  
    @Autowired  
    AdminLoginDetailService adminLoginDetailService;  
    @Autowired  
    private AuthenticationHandler authenticationHandler;  
    @Autowired  
    private CsrfTokenProvider csrfTokenProvider;  
  
    @Autowired  
    public void configure (AuthenticationManagerBuilder auth) throws Exception {  
        auth.authenticationProvider(adminAuthenticatorProvider);  
    }  
  
    @Bean  
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {  
        String loginMode = PropUtil.getProperty("system.login-mode");  
  
        /* 경로별 권한 설정 */        http  
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))  
            .authorizeHttpRequests((authorizeHttpRequests) -> authorizeHttpRequests  
                    .requestMatchers(new AntPathRequestMatcher("/bootstrap/**/**"), new AntPathRequestMatcher("/favicon/**"))  
                    .permitAll()  
                    .requestMatchers(PathRequest.toStaticResources().atCommonLocations()) // 정적 자원에 대해 허용, (/css/**, /js/**, /images/**, /webjars/**, /**/favicon.ico)  
                    .permitAll()  
                    .anyRequest().authenticated()  
            );  
  
        /* 로그인 모드별 Http 설정 */        if (loginMode.equals(Constant.LOGIN_MODE_SESSION)) {  
            http  
                .csrf(csrf -> csrf  
                        .csrfTokenRepository(csrfTokenProvider)  
                        .ignoringRequestMatchers(new AntPathRequestMatcher("/logout"))) // 로그아웃 경로에 대해서는 무시  
                .formLogin(formLogin -> formLogin  
                        .loginPage("/login") // 로그인  
                        .loginProcessingUrl("/login_request") // 로그인 처리할 경로  
                        .successHandler(authenticationHandler)  
                        .failureHandler(authenticationHandler)  
                        .permitAll()  
                )  
                .logout(logout -> logout  
                        .logoutRequestMatcher(new AntPathRequestMatcher("/logout", "GET"))  
                        .logoutSuccessUrl("/login")  
                        .deleteCookies("JSESSIONID")  
                )  
                .rememberMe(rememberMe -> rememberMe  
                    .key("payroll")  
                    .tokenValiditySeconds(60 * 60 * 24 * 7)  
                    .userDetailsService(adminLoginDetailService)  
                    .rememberMeParameter("remember-me")  
                )  
                .sessionManagement(sessionManagement -> sessionManagement  
                    .sessionFixation()  
                        .changeSessionId()  
                    .maximumSessions(1)  
//                    .expiredSessionStrategy()  
                    .maxSessionsPreventsLogin(false)  
                    .sessionRegistry(sessionRegistry())  
                    .expiredUrl("/login?expired")  
                );  
        } else if (loginMode.equals(Constant.LOGIN_MODE_JWT)) {  
            // Json Web Token 확인용 Filter        }  
        return http.build();  
    }  
  
    // CORS 설정 Bean    
    @Bean  
    public CorsConfigurationSource corsConfigurationSource() {  
        CorsConfiguration configuration = new CorsConfiguration();  
        configuration.setAllowedOrigins(Arrays.asList("*"));  
        configuration.setAllowedMethods(Arrays.asList("GET","POST", "PUT", "DELETE"));  
        configuration.setAllowedHeaders(Arrays.asList("X-Requested-With", "Content-Type", "Authorization", "X-CSRF-TOKEN"));  
        configuration.setAllowCredentials(false);  
        configuration.setMaxAge(3600L);  
  
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();  
        source.registerCorsConfiguration("/**", configuration);  
        return source;  
    }  
  
    @Bean  
    public SessionRegistry sessionRegistry() {  
        return new SessionRegistryImpl();  
    }  
  
    @Bean  
    public ServletListenerRegistrationBean<HttpSessionEventPublisher> httpSessionEventPublisher() {  
        return new ServletListenerRegistrationBean<>(new HttpSessionEventPublisher());  
    }  
}
```

기존 Spring Security 설정 방식인 `WebSecurityConfigurerAdapter`을 상속받아 config하는 방식에서
`SecurityFilterChain`의 Bean을 메서드 체이닝 방식으로 구현하는 형태로 변경되었다.


세션 기반, 토큰 기반 로그인의 구현 용도로 프로퍼티 상의 Login Mode값을 받아서 분기되게 해놓았으나 실제로는 그렇게 쓸 일이 없어보임 (토큰이면 토큰, 세션이면 세션 하나로 통일)

[Spring Security 6.2 Docs](https://docs.spring.io/spring-security/reference/index.html)