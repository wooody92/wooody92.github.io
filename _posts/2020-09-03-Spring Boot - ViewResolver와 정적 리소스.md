---
title:  "Spring Boot - ViewResolver와 정적 리소스"
excerpt: "스프링 Web MVC : ContentsNegotiationViewResolver & 정적 리소스"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - ViewResolver
  - ResourceHandler
last_modified_at: 2020-09-03. 16:00:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 3부 : ViewResolver

### ContentsNegotiationViewResolver

- 스프링 부트가 제공하는 `viewResolver` 중 하나인 `ContentsNegotiationViewResolver`에 대해 알아보자.

- 클라이언트가 요청하는 `HTTP Accept Header`에 따라서 어떤 타입의 view 또는 응답을 줘야하는지 달라질 수 있다. (클라이언트가 어떤 뷰를 원하는지 판단하는 가장 좋은 정보는 Accept header를 확인하는 것이다.)

- 우리는 따로 특별한 설정 없이도 클라이언트에 요청에 따라 자동으로 적절한 응답을 준다.

- 어떻게? 많은 기능들이 상호작용으로 처리하지만, 대표적으로 `Spring MVC`의 `ContentsNegotiationViewResolver`가 `Accept header`를 보고 판단하여 자동으로 처리해준다.

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(UserController.class)
  public class UserControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void createUser_XML() throws Exception {
          String userJson = "{\"username\":\"henry\", \"password\":\"123\"}";
          mockMvc.perform(post("/users/create")
                  .contentType(MediaType.APPLICATION_JSON_UTF8)
                  .accept(MediaType.APPLICATION_XML) // accept header
                  .content(userJson))
              .andExpect(status().isOk())
              .andExpect(xpath("/User/username").string("henry"))
              .andExpect(xpath("/User/password").string("123"));
      }
  }
  ```

  ```xml
  // xml 응답은 아래 의존성을 추가해야 한다. json은 기본적으로 내장되어 있다.
  <dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.11.2</version>
  </dependency>
  ```

  

<br>



## Spring Web MVC 4부 : 정적 리소스 지원

### 정적 리소스와 기본 리소스 위치

- 클라이언트에서 요청할 때 이미 만들어진 `Resource`를 그대로 제공하는 것이다.

- `classpath:/static`

- `classpath:/public`

- `classpath:/resources/`

- `classpath:/META-INF/resources`

  - 예시 : `/hello.html` -> `/static/hello.html`

- 기본적으로 `root`주소에 붙어 요청하는데 `spring.mvc.static-path-pattern`으로 변경가능하다.

  ```java
  // before
  http://localhost:8080/hello.html
  
  // after
  spring.mvc.static-path-pattern=/static/**
  http://localhost:8080/static/hello.html
  ```



### Custom ResourceHandler

- 기본 리소스 위치를 제외하고 내가 `resource` 디렉토리에 커스텀한 디렉토리를 만들고 정적파일을 생성하면 못 읽어온다. (`resource - henry - test.html`)

- 기존의 스프링 부트가 제공하는 리소스 핸들러는 유지하면서, 나만의 커스텀한 리소스 핸들러를 `WebMvcConfigurer - addResourceHandlers`를 이용하여 추가 할 수 있다.

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
  
      @Override
      public void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/**")
              .addResourceLocations("classpath:/henry/")
              .setCachePeriod(20);
      }
  }
  ```

