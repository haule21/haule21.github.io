---
layout: posts
permalink: /:categories/:title
title: "Spring Security 6 그리고 Spring Boot 3"
categories: spring
---


## Deprecated 
- WebSecurityConfigureAdapter
- EnableGlobalMethodSecurity
- antMatchers
- authorizeReuqests

## Configuration
- Spring Security를 기본 설정으로 활성화 하려면, servlet Filter를 만들고 Bean의 이름을 securityFilterChain 으로 해야됨
```
public class SecurityConfig{

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http.build();
    }
}
```
- Spring Security 적용 예시
```
@Configuration
public class SecurityConfig{

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.csrf().disable() // csrf 비 활성화
            .authorizeHttpRequests(
                (auth) -> auth.anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());
        return http.build();
    }
}
```

- [Stackoverflow에 올라온 답변](https://stackoverflow.com/questions/57574981/what-is-httpbasic-method-in-spring-security)

`httpBasic 을 왜 사용하는 걸까?`
- httpBasic이 호출이 되면 Authorization 요청 헤더가 전달한 값을 사용하여 요청을 인증하도록 Spring에게 알려준다. 인증되지 않은 요청일 경우에는 401 에러를 발생시킨다. (error message는 Unauthorized)
- httpBasic을 호출하면 BasicAuthenticationFilter 인스턴스가 필터 체인에 추가 되고 Spring Security 방식으로 인증하려고 시도한다. 인증에 성공할 경우 SecurityContextHolder에 배치 되어 이후 인증 용도로 사용할 수 있다.

대략적으로..
- 사용을 하게 되면 base64로 encrypt 돼 Authorization 헤더 값에 추가 된다. spring은 우리가 전달한 값을 사용하여 인증하도록 알려줬기 때문에 헤더를 확인하고 인증 된 요청인지 확인하게 된다!


## @EnableMethodSecurity
- 특정 페이지에 대한 특정 권한이 필요할 경우 사용하는 Annotation
- Annotation 을 SecurityConfig에 추가 해준다.
- PreAuthorize, PostAuthorize, PreFilter, PostFilter 등 사용 할 수 있다.

```
@PreAuthorize("hasRole('ADMIN')")
@RequestMapping(value="/needAdminAuth", method=RequestMethod.POST)
public ...
```

## User 등록 방법
- DB를 통해 불러오는 방법과 Memory에 사용자를 등록해서 검증하는 방법이 있다.

- Memory
```
public UserDetailsService userDetailService(){
    UserDetails user = User.builder()
                        .username("user")
                        .password("user")
                        .roles("USER")
                        .build();

    UserDetails admin = User.builder()
                        .username("admin")
                        .password("admin")
                        .roles("ADMIN")
                        .build();

    // InMemoryUserDetailsManager(UserDetails... users)
    return new InMemoryUserDetailsManager(user, admin);
}
```

- DB
```
public UserDetailsManager users(DataSource dataSource) {
    JdbcUserDetailsManager users = new JdbcUserDetailsManager(dataSource);

    // userid, user_role을 가져오는 쿼리문을 작성해주면 된다.
    users.setAuthoritiesByUsernameQuery("SELECT USERID, ROLE_NAME FROM USERS USERID = ?");

    // userid, password, 활성화 여부(true or false) 가져오는 쿼리문을 작성해주면 된다.
    users.setUsersByUsernameQuery("SELECT USERID, PASSWORD, IF(DELETED = 'N', 'TRUE', 'FALSE') AS ENABLED FROM USERS WHERE USERID = ?");

    return users;
}
```
** 그래서 form 작성은 어떻게 하면 될까? **

```
@Configuration
public class SecurityConfig{

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.csrf().disable() // csrf 비 활성화

            .authorizeHttpRequests(
                (auth) -> auth.anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .formLogin()

                // form의 값과 동일하게 작성해야 정상적으로 작동 된다.
                .usernameParameter("username")
                .passwordParameter("password")

                // 로그인에 성공 했을 시 이동 Url
                .defaultSuccessUrl("/")
                .loginPage("/login").permitAll()

                // 이 Url를 통해서 인증을 진행 한다. 따로 Controller에서 등록할 필요 없다.
                .loginProcessingUrl("/loginProc").permitAll()

            .and()

            // 로그아웃!
            .logout().permitAll().and()

            // 예외처리 기능! (.authenticationEntryPoint() !!인증예외처리 , .accessDeniedHandler() !!인가예외처리 )
            // 
            .exceptionHandling().accessDeniedPage("/404")
            .http;

        return http.build();
    }
}
```


버전에 따라 적용 방법이 조금씩 차이가 있어서 해맸는데 마침 좋은 영상이 있어서 글로 간단히 정리 해서 남긴다.
- [Learn Spring Security 6 with Spring Boot 3](https://www.youtube.com/watch?v=bB6A490Uh5M)


## 로그인, 로그아웃 Controller

```
    @RequestMapping("/login")
	public ModelAndView login(ModelAndView mv) {

		mv.setViewName("/login");
		return mv;
	}
```

```
    @RequestMapping(value = "/logout", method = RequestMethod.GET)
    public ModelAndView logout(
        HttpServletRequest request, 
        HttpServletResponse response, 
        ModelAndView mv) throws Exception {
    	
    	Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        if (auth != null) {
            new SecurityContextLogoutHandler().logout(request, response, auth);
        }
        
        mv.setViewName("redirect:login");
        return mv;
    }
    
```

이 글을 보는 누군가도 참고가 됐으면 좋겠다.