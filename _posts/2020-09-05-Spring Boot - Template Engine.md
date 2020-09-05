---
title:  "Spring Boot - Template Engine"
excerpt: "스프링 Web MVC : Thymeleaf와 HtmlUnit"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - Template Engine
  - Thymeleaf
  - HtmlUnit
last_modified_at: 2020-09-05. 16:00:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 7부 : Thymeleaf

> 스프링 부트를 사용한 스프링 웹 MVC 애플리케이션으로 동적 컨텐츠를 생성하는 방법에 대해 알아보자.

### Template Engine

- 템플릿 엔진은 `View`만 만드는게 쓰이는게 아니고 여러 용도로(코드 제너레이션 등) 쓰일 수 있으나, 주로 `View`를 만드는데 사용한다.
- `View`를 만드는데 템플릿 엔진을 왜 쓸까? 기본적인 템플릿은 같은데 그 안에 들어가는 값들이 경우에 따라 달라지므로 정적 컨텐츠를 사용할 수 없고 동적으로 사용해야하기 때문
- 스프링 부트가 자동 설정을 지원하는 템플릿 엔진
  - `FreeMarker`,  `Groovy`, `Thymeleaf`, `Mustache`
- `JSP(Java Server Page)`는 자동설정을 지원하지 않기도하고 권장하지도 않는다.



### JSP를 권장하지 않는 이유

- `JSP`는 자동설정을 지원하지 않기도하고 권장하지도 않는다. 왜? 스프링이 지향하는 철학과 거리가 있다.
- 스프링 부트는 독립적으로 실행가능한 임베디드 톰캣으로 앱을 빠르고 쉽게 만들어 배포하길 바란다. 그러나 `JSP`를 사용하면 `jar` 패키징을 사용하지 못하고 `war` 패키징 해야한다.
- `Undertow`와 같은 최신 서블릿 컨테이너는 `JSP` 지원하지도 않는다.
- 또한 `JSP` 의존성을 추가하면 스프링 내부에서 의존성 문제가 생길 수도 있다.
- 위 제약사항들 때문에 잘 사용하지 않는다.



### Thymeleaf

- [https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html](https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html)

- 실제 본문(body) 오는 렌더링 된 결과를 확인 할 수 있다.

- 렌더링 자체를 서블릿 엔진이 해야하기 때문에 `JSP`는 못한다. (서블릿 엔진이 템플릿을 완성시키므로)

- 타임리프의 엔진은 서블릿 컨테이너에 독립적인 엔진이므로 뷰에 렌더링되는 결과까지 확인 할 수 있다.

- 타임리프를 사용하기 위해서는 `html`에 `<html lang="en" xmlns:th="http://www.thymeleaf.org">`와 같이 추가해주어야 한다.

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
  <h1 th:text="${name}">Name</h1> // name model이 있으면 name을 반환, 없으면 Name을 반환한다.
  </body>
  </html>
  ```

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(SampleController.class)
  public class SampleControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void hello() throws Exception {
          // 요청 : "/hello"
          // 응답
          // ~ 모델 name : henry
          // ~ 뷰 이름 : hello
          mockMvc.perform(get("/hello"))
              .andExpect(status().isOk())
              .andDo(print())
              .andExpect(view().name("hello")) // view : hello.html 있는지
              .andExpect(model().attribute("name", is("henry"))) // model : name - henry의 model이 있는지
              .andExpect(content().string(containsString("henry"))); // view
      }
  }
  ```

  ```java
  @Controller
  public class SampleController {
  
      @GetMapping("/hello")
      public String hello(Model model) {
          model.addAttribute("name", "henry");
          return "hello";
      }
  }
  ```

  ```java
  MockHttpServletResponse:
             Status = 200
      Error message = null
            Headers = [Content-Language:"en", Content-Type:"text/html;charset=UTF-8"]
       Content type = text/html;charset=UTF-8
               Body = <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
  <h1>henry</h1> // 렌더링 된 모델의 결과값도 볼 수 있다.
  </body>
  </html>
      Forwarded URL = null
     Redirected URL = null
            Cookies = []
  ```

  

<br>

## Spring Web MVC 8부 : HtmlUnit

### HtmlUnit

- `htmlunit`과 `htmlunit-driver`의 의존성을 추가해야 사용 할 수 있다.

- 위 방식처럼 `mockMvc`를 사용하면 모델과 뷰 이름 등 확인할 수 있지만, `htmlUnit`은 `html`에 조금 더 특화되어 있는 테스트를 작성 할 수 있다. 취향차이이므로 선택해서 사용하면 된다.

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(SampleController.class)
  public class SampleControllerTest {
  
      @Autowired
      WebClient webClient;
  
      @Test
      public void hello() throws IOException {
          HtmlPage page = webClient.getPage("/hello");
          HtmlHeading1 h1 = page.getFirstByXPath("//h1");
          assertThat(h1.getTextContent()).isEqualToIgnoringCase("henry");
      }
  }
  ```

