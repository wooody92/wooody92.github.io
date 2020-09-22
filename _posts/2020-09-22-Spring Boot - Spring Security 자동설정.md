---
title:  "Spring Boot - Spring Security 자동설정"
excerpt: "스프링 Security : Starter-Security"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Security
  - SecurityAutoConfiguration
  - DefaultAuthenticationEventPublisher
  - UserDetailsServiceAutoConfiguration
last_modified_at: 2020-09-22. 16:20:00

---

<br>

# 스프링 부트 활용

## Spring Security 1부 : Starter-Security

> 스프링 부트와 스프링 세큐리티를 연동하는 기능에 대해 알아보자.

### 개요

- `spring-boot-starter-security` 의존성을 적용하면 자동설정으로 기본 적인 기능들이 제공된다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
    <version>2.3.1.RELEASE</version>
  </dependency>
  ```



### Spring Security

- 웹 시큐리티, 메소드 시큐리티, 다양한 인증 방법 지원(LDAP, 폼 인증, Basic 인증, OAuth, ...)

- 의존성만 추가해도 모든 요청이 전부 시큐리티 인증을 필요로 하게 한다.

  ```java
  @Controller
  public class HomeController {
  
      @GetMapping("/hello")
      public String hello() {
          return "hello";
      }
  
      @GetMapping("/my")
      public String my() {
          return "my";
      }
  }
  ```

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(HomeController.class)
  public class HomeControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void hello() throws Exception {
          mockMvc.perform(get("/hello"))
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(view().name("hello"));
      }
  
      @Test
      public void my() throws Exception {
          mockMvc.perform(get("/my").accept(MediaType.TEXT_HTML))
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(view().name("my"));
      }
  }
  ```

- 기존에 문제없이 통과하던 테스트가 인증으로 인해 통과하지 못한다. `/login`으로 자동으로 리다이렉트 된다.

  ```java
  // result - hello
  MockHttpServletResponse:
             Status = 401
      Error message = Unauthorized
            Headers = [WWW-Authenticate:"Basic realm="Realm"", X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY"]
  
  // result - my
  MockHttpServletResponse:
             Status = 302
      Error message = null
            Headers = [X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY", Location:"http://localhost/login"]
  ```

- 애플리케이션을 실행 후 localhost:8080으로 접속하면 아래와 같이 인증화면으로 리다이렉트 된다. (인증정보가 없어서 스프링 시큐리티에서 만들어준 페이지로 리다이렉트)

  <img width="808" alt="security1" src="https://user-images.githubusercontent.com/58318041/93853064-1ef4d800-fcee-11ea-84a9-5dbb2b921565.png">

- 기본적으로 아이디는 `user`이고 비밀번호는 스프링 부트에서 자동으로 생성해준다.

  ```java
  Using generated security password: 0afbb8a1-347d-41ee-9aab-d540782ade2c
  ```

- 인증으로 깨진 테스트를 아래와 같이 의존성과 `@WithMockUser` 추가로 정상동작하게 할 수 있다.

  ```xml
  <dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-test</artifactId>
    <version>5.3.2.RELEASE</version>
    <scope>test</scope>
  </dependency>
  ```

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(HomeController.class)
  public class HomeControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      @WithMockUser
      public void hello() throws Exception {
          mockMvc.perform(get("/hello"))
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(view().name("hello"));
      }
  
      @Test
      @WithMockUser
      public void my() throws Exception {
          mockMvc.perform(get("/my").accept(MediaType.TEXT_HTML))
              .andDo(print())
              .andExpect(status().isOk())
              .andExpect(view().name("my"));
      }
  }
  ```

  

### 의존성만 추가했다고 왜 바뀔까?

- 스프링 부트가 제공하는 스프링 시큐리티 자동설정 중 하나이다.

- 모든 요청이 스프링 시큐리티로 인해 인증을 필요로 하게 한다.

- 또한 `basic authentication`과 `pom 인증`이 둘다 적용된다.

- `basic 인증`은 `Accept header` 에 따라 달라진다.

  - `Accept header` : 이 요청이 원하는 응답의 형태를 말한다.

- 이것을 별다른 설정을 하지 않는다면 아래와 같이 `basic authentication` 관련된 헤더가 응답으로 담겨져 나오고 이는 브라우저가 내장하고 있는 베이직 폼을 띄운다.

  ```java
  Headers = [WWW-Authenticate:"Basic realm="Realm""...]
  ```

- 보통의 경우는 `text/html`로 설정한다.



### 스프링 부트가 제공하는 설정파일

- `SecurityAutoConfiguration` 내부에 `DefaultAuthenticationEventPublisher`이 등록이 되어있다.

- `DefaultAuthenticationEventPublisher` 클래스를 까보면, 여러 경우에 대해 이벤트를 발생시키고 우리는 그 이벤트 핸들러를 등록해서 유저의 상태를 변경하는 등 여러가지 일들을 할 수 있다. (다양한 인증 핸들러 등록 가능)

- 이는 스프링 부트가 아니고 스프링 시큐리티에서 제공하는 기능이다.

  ```java
  public DefaultAuthenticationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
    this.logger = LogFactory.getLog(this.getClass());
    this.exceptionMappings = new HashMap();
    this.applicationEventPublisher = applicationEventPublisher;
    this.addMapping(BadCredentialsException.class.getName(), AuthenticationFailureBadCredentialsEvent.class);
    this.addMapping(UsernameNotFoundException.class.getName(), AuthenticationFailureBadCredentialsEvent.class);
    this.addMapping(AccountExpiredException.class.getName(), AuthenticationFailureExpiredEvent.class);
    this.addMapping(ProviderNotFoundException.class.getName(), AuthenticationFailureProviderNotFoundEvent.class);
    this.addMapping(DisabledException.class.getName(), AuthenticationFailureDisabledEvent.class);
    this.addMapping(LockedException.class.getName(), AuthenticationFailureLockedEvent.class);
    this.addMapping(AuthenticationServiceException.class.getName(), AuthenticationFailureServiceExceptionEvent.class);
    this.addMapping(CredentialsExpiredException.class.getName(), AuthenticationFailureCredentialsExpiredEvent.class);
    this.addMapping("org.springframework.security.authentication.cas.ProxyUntrustedException", AuthenticationFailureProxyUntrustedEvent.class);
    this.addMapping("org.springframework.security.oauth2.server.resource.InvalidBearerTokenException", AuthenticationFailureBadCredentialsEvent.class);
  }
  ```

- `SpringBootWebSecurityConfiguration` - `WebSecurityConfigurerAdapter`를 살펴보면 스프링 시큐리티는 스프링 부트가 제공해주는 시큐리티 기능들을 그대로 사용한다.

-  `UserDetailsServiceAutoConfiguration`에서 스프링 시큐리티가 `AuthenticationManager.class, AuthenticationProvider.class, UserDetailsService.class` 클래스가 없다면 기본 유저아이디와 패스워드를 제공한다.(`UserDetailsManager`)

- `application.property` 추가로 원하는 유저정보로 만들 수 있다.

  ```java
  spring.security.user.name=henry
  spring.security.user.password=1234
  ```

- 스프링 시큐리티를 적용하는 프로젝트들은 프로젝트만의 `UserDetailsService`를 등록하게 되어있다. 그래서 사실 스프링 부트가 자동설정으로 제공하는 스프링 시큐리티 관련 기능들은 잘 안쓴다.

