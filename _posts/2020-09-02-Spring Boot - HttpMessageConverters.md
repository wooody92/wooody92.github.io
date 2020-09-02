---
title:  "Spring Boot - Spring Web MVC와 HttpMessageConverters"
excerpt: "스프링 Web MVC : 소개 & HttpMessageConverters"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - HttpMessageConverters
last_modified_at: 2020-09-02. 18:00:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 1부 : 소개

### 소개

- 스프링 부트 환경에서 `Spring Web MVC` 기능을 별 다른 설정없이 기본 설정만으로 사용 할 수 있다.

- 어떻게? 자동 설정 (`AutoConfig`)이 적용되어서 `web-mvc-autoconfiguration`을 가져온다.

  ```java
  org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration
  ```

### WebMvcAutoConfiguration

- `HiddenHttpMethodFilter` : 스프링 3.0부터 제공해주는 필터로, `PUT`, `DELETE`, `PATCH` 요청을 `_method`를 통해 어떤 값인지 받아 올 수 있고 `@Controller` 단에 맵핑을 해준다.
- `HttpPutFormContentFilter` : 기존 서블릿 스펙은 `HTTP POST`의 폼 데이터만 보낼 수 있게 정의되어 있는데, `PUT`, `PATCH` 도 특정 컨텐츠 타입(`application/x-www-form-urlencoded`)으로 폼 데이터를 보내오면 `POST`에서 꺼낼 수 있는 것처럼 `Wrapping`을 해준다.
- `WebMvcProperties` : mvc 관련 properties



### WebMvcConfigurer

- 스프링 부트가 제공해주는 기능을 모두 사용하면서 추가적으로 확장을 하고 싶다면 `WebMvcConfigurer`를 이용하면 된다.

- `WebMvcConfigurer` 인터페스가 제공하는 콜백 메서드를 사용하여 커스터마이징 할 수 있다.

  - 컨버터 추가, 인터셉터 추가, CorsMapping, 리소스핸들러 등
  - `@EnableWebMvc`를 설정하면 스프링 부트에서 제공하는 기능을 모두 끄고 모두 내가 설정해야 한다.

  ```java
  @Configuration
  //@EnableWebMvc
  public class WebConfig implements WebMvcConfigurer {
  }
  ```

<br>



## 스프링 웹 MVC 2부 : HttpMessageConverters

### 개념

- 스프링 프레임워크에서 제공하는 인터페이스이며 `Spring Mvc`의 일부이다.

- `HTTP` 요청 본문으로 들어오는 내용을 객체로 변환하거나, 객체를 `HTTP` 응답 시  변환해주는 역할을 한다.

- 기본적으로 `Composition`(`User`와 같이 필드값이 여러개 인 클래스) 타입일떄는 `JSON`으로 변환한다. (`JsonMessageConverter`)

- 단일 객체의 경우 `StringMessageConverter`를 사용하여 변환한다. (`String`, `int` 등)

- `@RestController`를 사용하면 `@ResponseBody`를 생략할 수 있다.

- `@Controller`를 사용한다면 아래와 같이 명시해줘야 `MessageConverter`가 적용이 된다. 생략하면 `viewNameResolver`를 타게된다.

  ```java
  @RestController
  public class UserController {
  
      @PostMapping("/users/create")
      public @ResponseBody User create(@RequestBody User user) {
          return user;
      }
  }
  ```

- Json 응답 테스트 코드

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(UserController.class)
  public class UserControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void createUser_JSON() throws Exception {
          String userJson = "{\"username\":\"henry\", \"password\":\"123\"}";
          mockMvc.perform(post("/users/create")
              .contentType(MediaType.APPLICATION_JSON_UTF8)
              .accept(MediaType.APPLICATION_JSON_UTF8)
              .content(userJson))
          .andExpect(status().isOk())
          .andExpect(jsonPath("$.username", is(equalTo("henry"))))
          .andExpect(jsonPath("$.password", is(equalTo("123"))));
      }
  }
  ```



