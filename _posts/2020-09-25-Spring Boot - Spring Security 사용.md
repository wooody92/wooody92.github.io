---
title:  "Spring Boot - Spring Security 사용"
excerpt: "스프링 Security : Security 설정 커스터마이징"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Security
  - WebSecurityConfigurerAdapter
  - UserDetailsService
  - passwordEncoder
last_modified_at: 2020-09-30. 13:30:00

---

<br>

# 스프링 부트 활용

## Spring Security 2부 : Security 설정 커스터마이징

> 스프링 시큐리티의 다양한 설정 방법에 대해 대해 알아보자.
>
> 1. url path 별로 페이지 접근 허용 및 차단 설정
> 2. 사용자 정보로 로그인 구현
> 3. 비밀번호 인코딩하여 저장하기

### 개요

- `spring-boot-starter-security` 의존성을 추가하면 자동 설정으로 인해 전체 페이지에 접근에 인증을 요구하게 된다.
- `WebSecurityConfigurerAdapter` 설정으로 특정 페이지(`url path`)만 인증(로그인)을 요구하도록 설정 할 수 있다.



### # 1단계 : 엔드포인트 별로 페이지 접근 권한 설정하기

- `WebSecurityConfigurerAdapter` 클래스를 상속 받아 `configure(HttpSecurity http)` 메서드를 오버라이드 하여 설정할 수 있다.

- `WebSecurityConfigurerAdapter` 클래스를 상속받은 `SecurityConfig`가 빈으로 등록되면, Security auto configuration(자동 설정)은 더이상 동작하지 않는다.

- 아래 예제는 `base url`과 `/hello`에는 모두 접근 가능하게 하고, 그 외 `url path`에는 formLogin 인증을 필요하게 한다.

  - 인증정보가 없으면 로그인 페이지에 걸린다.
  - 왜 formLogin에 걸릴까? accept header에 html이 있으니까
  - 그럼 html이 없는 경우에는? httpBasic에 걸릴 것 이다.

  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http.authorizeRequests()
                  .antMatchers("/", "/hello").permitAll()
                  .anyRequest().authenticated()
                  .and()
              .formLogin()
                  .and()
              .httpBasic();
      }
  }
  ```

  

### 2단계 사용자 정보로 로그인 구현하기

- `UserDetailsService` 인터페이스를 상속받아 구현한다.

- 주로 유저 정보를 관리하는 서비스 계층에서 인터페이스를 구현한다.

  - 별도로 클래스를 생성하여 관리해도 상관은 없으나 `UserDetailsService`의 구현체는 빈으로 등록되어야 한다.(`@Component`)

- `UserDetailsService` 타입이 빈으로 등록되면 더이상 자동설정으로 생성된 유저가 만들어지지 않는다.

- `UserDetailsService` 인터페이스로 구현한 방식으로 로그인 처리가 된다.

  - 핵심 인터페이스인 오버라이드 된 `loadUserByUsername` 메서드로 페이지에서 입력한 아이디와 패스워드 등 유저정보를 검증한다.

- 아래는 저장되어 있는 유저 정보를 기반으로 로그인 처리를 구현한 코드이다.

  ```java
  @Service
  public class AccountService implements UserDetailsService {
  
      @Autowired
      private AccountRepository accountRepository;
  
      public Account create(String username, String password) {
          Account account = new Account();
          account.setUsername(username);
          account.setPassword(password);
          return accountRepository.save(account);
      }
  
      @Override
      public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
          Optional<Account> byUsername = accountRepository.findByUsername(username);
          Account account = byUsername.orElseThrow(() -> new UsernameNotFoundException(username));
          return new User(account.getUsername(), account.getPassword(), authorities());
      }
  
      private Collection<? extends GrantedAuthority> authorities() {
          return Arrays.asList(new SimpleGrantedAuthority("ROLE_USER"));
      }
  }
  ```

- 하지만 패스워드 인코딩 설정을 안했기 때문에 로그인이 되지 않고 에러가 발생한다.



### 3단계 패스워드 인코딩 설정하여 암호화하기

- `passwordEncoder` 클래스를 빈으로 등록하여 원하는 방식의 엔코더로 패스워드를 암호화 할 수 있다.

- 서비스 계층에서도 `PasswordEncoder`를 주입받아 DB에 암호화하여 저장할 수 있다.

  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http.authorizeRequests()
                  .antMatchers("/", "/hello").permitAll()
                  .anyRequest().authenticated()
                  .and()
              .formLogin()
                  .and()
              .httpBasic();
      }
  
      @Bean
      public PasswordEncoder passwordEncoder() {
          return PasswordEncoderFactories.createDelegatingPasswordEncoder();
      }
  }
  ```

  ```java
  @Service
  public class AccountService implements UserDetailsService {
  
      @Autowired
      private AccountRepository accountRepository;
  
      @Autowired
      private PasswordEncoder passwordEncoder;
  
      public Account create(String username, String password) {
          Account account = new Account();
          account.setUsername(username);
          account.setPassword(passwordEncoder.encode(password));
          return accountRepository.save(account);
      }
  
      @Override
      public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
          Optional<Account> byUsername = accountRepository.findByUsername(username);
          Account account = byUsername.orElseThrow(() -> new UsernameNotFoundException(username));
          return new User(account.getUsername(), account.getPassword(), authorities());
      }
  
      private Collection<? extends GrantedAuthority> authorities() {
          return Arrays.asList(new SimpleGrantedAuthority("ROLE_USER"));
      }
  }
  ```

  ```java
  // result - before
  Account(id=1, username=henry, password=1234)
    
  // result - after
  Account(id=1, username=henry, password={bcrypt}$2a$10$vEpYSx3B0vX1hxE3kblmGuaEdRIlr6dFYKFCngjANgsf0haHdKIxy)
  ```

