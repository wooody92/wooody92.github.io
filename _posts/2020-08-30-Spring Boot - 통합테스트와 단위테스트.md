---
title:  "Spring Boot - 통합테스트와 단위테스트"
excerpt: "스프링 부트 활용 : SpringBootTest와 WebEnvironment"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - SpringBootTest
  - WebEnvironment
  - MockMvc
  - TestRestTemplate
  - WebTestClient
  - WebMvcTest
  - OutputCapture
last_modified_at: 2020-08-30. 20:00:00

---

<br>

# 스프링 부트 활용

## 통합 테스트(@SpringBootTest)

### @SpringBootTest

- `SpringBootTest`는 단위테스트와 같이 기능 검증이 아닌, 스프링에서 실제 운영 환경과 같이 전체 플로우가 제대로 동작하는지 보기 위한 통합테스트이다.

- `@SpringBootTest`가 동작하면  `@SpringBootApplication`을 찾아가서 모든 빈을 스캔한다. 즉, 모든 빈을 로드하는 통합 테스트이기 때문에 무겁다.

- `spring-boot-starter-test` 의존성을 추가하면 테스트에 필요한 대부분의 라이브러리가 포함되어 있다. (`JUnit`, `assertJ`, `mockito` 등)

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
  </dependency>
  ```

- 가장 기본형태의 테스트 코드는 아래와 같이 `@RunWith(SpringRunner.class)`와 같이 사용해야 한다. 

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest
  public class SampleControllerTest {
  }
  ```

  ```java
  @RestController
  public class SampleController {
  
      @Autowired
      private SampleService sampleService;
  
      @GetMapping("/hello")
      public String hello() {
          return "hello " + sampleService.getName();
      }
  }
  ```

  ```java
  @Service
  public class SampleService {
  
      public String getName() {
          return "henry";
      }
  }
  ```

<br>

## webEnvironment

### MockMvc

- `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)`와 같이 설정하면 `ServletContainer`를 테스트용으로 띄우지않고 서블릿을 mocking 한 것이 동작한다. (내장 톰캣이 구동되지 않는다.)

- `DispatcherServlet`을 만들어지긴하나 `mockup`이 된 서블릿에 접근하려면 `MockMvc client`로 접근한다.

- 아래는 `WebEnvironment` 환경이(테스트의 웹 환경) `MOCK`으로 설정되어 있을때 테스트하는 예제이다.

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = WebEnvironment.MOCK)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @Test
      public void hello() throws Exception {
          mockMvc.perform(get("/hello"))
              .andExpect(status().isOk())
              .andExpect(content().string("hello henry"))
              .andDo(print());
      }
  }
  ```

  ```java
  // result
  
  MockHttpServletRequest:
        HTTP Method = GET
        Request URI = /hello
         Parameters = {}
            Headers = []
               Body = null
      Session Attrs = {}
  
  Handler:
               Type = dev.springboot.study.sample.SampleController
             Method = dev.springboot.study.sample.SampleController#hello()
  
  Async:
      Async started = false
       Async result = null
  
  Resolved Exception:
               Type = null
  
  ModelAndView:
          View name = null
               View = null
              Model = null
  
  FlashMap:
         Attributes = null
  
  MockHttpServletResponse:
             Status = 200
      Error message = null
            Headers = [Content-Type:"text/plain;charset=UTF-8", Content-Length:"11"]
       Content type = text/plain;charset=UTF-8
               Body = hello henry
      Forwarded URL = null
     Redirected URL = null
            Cookies = []
  ```

  - 위 예제코드에서는 `status`와 `content` 상태값을 확인했다. 위 `print`로 출력된 부분의 내용들의 대부분 `Assertion`으로 검증 할 수 있다.
  - 그 외에도 어떤 컨트롤러, 어떤 메서드를 사용했는지 맵핑을 확인 할 수도 있다.



### TestRestTemplate

- `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`와 같이 설정하면 실제로 서블릿이 동작한다. (내장 톰캣을 사용한다.)

- 이 경우에는 `MockMvc`를 사용하는 것이 아니고, `TestRestTemplate` 또는 `TestWebClinet`를 사용한다.

- 아래 예제는 `TestRestTemplate`를 사용하여 실제로 내장 톰캣 서버에 요청을 보내고 응답을 받아 확인한 것이다.

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Autowired
      TestRestTemplate testRestTemplate;
  
      @Test
      public void hello() throws Exception {
          String result = testRestTemplate.getForObject("/hello", String.class);
          assertEquals("hello henry", result);
      }
  }
  ```

- 위 예제 코드의 경우 `Controller`를 테스트하는데 `Service`까지 들어가야해서 테스트가 무겁다. 이 경우 아래와 같이 `@MockBean`을 이용하여 해결 할 수 있다.

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Autowired
      TestRestTemplate testRestTemplate;
  
      @MockBean
      SampleService mockSampleService;
  
      @Test
      public void hello() throws Exception {
          // 테스트 환경에서 mockSampleService.getName()이 호출되면 "wooody92"로 응답한다.
          when(mockSampleService.getName()).thenReturn("wooody92");
          String result = testRestTemplate.getForObject("/hello", String.class);
          assertEquals("hello wooody92", result);
      }
  }
  ```

  - `@MockBean`을 사용하면 `ApplicationContext` 안에 들어있는 `SampleService Bean`을 테스트 환경에서 만든 `MockBean` 객체로 교체한다.
  - 그래서 테스트 환경에서 `Controller`는 원본이 아닌 `mockSampleService`를 사용하게 된다.
  - 모든 `@Test` 마다 자동으로 리셋된다.



### WebTestClient

- `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`로 실제 서블릿 동자 환경에서 사용한다.

- `Java5`에서 `Spring MVC WebFlux`에 새로 추가된 `RestClient` 중 하나이다.

- 기존에 사용하던 `RestClient`는 동기방식으로 요청보내고 끝날 때 까지 대기 후 다음 요청 보내는 방식으로 동작했다.

- `WebClient`는 비동기 방식으로 요청을 보내고 기다리지 않고 요청에 대한 응답이 오면 콜백(이벤트)이 오면 콜백을 실행하는 방식으로 동작한다.

-  테스트 환경에서도 `WebTestClient`를 사용하여 `WebClient`와 동일한 `api`를 사용 할 수 있다.

- `WebTestClient`를 사용하기 위해 `webFlux` 의존성을 추가한다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
  </dependency>
  ```

- 아래는 `WebTestClient`를 사용한 테스트 예제이다. 비동기 사용 목적이 아니더라도 체이닝 방식의 `api`가 편리하여 사용하기 유용하다.

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Autowired
      WebTestClient webTestClient;
  
      @MockBean
      SampleService mockSampleService;
  
      @Test
      public void hello() throws Exception {
          when(mockSampleService.getName()).thenReturn("wooody92");
          webTestClient.get().uri("/hello").exchange()
              .expectStatus().isOk()
              .expectBody(String.class).isEqualTo("hello wooody92");
      }
  }
  ```

<br>

## 단위 테스트

### @WebMvcTest

- `Controller` 하나, 빈 하나만 테스트하기 때문에 가볍다.

- 웹계층과 관련된 항목들만 빈으로 등록하고, 다른 항목들은 빈으로 등록되지 않는다.

  - `@Controller`, `@ControllerAdvice`, `@JsonComponent`, `Converter`, `GenericConverter`, `Filter`, `WebMvcConfigurer` and `HandlerMethodArgumentResolver`
  - `Service`, `Repository`와 같이 웹 계층 아래 항목들은 등록이 되지 않기 때문에 의존성 연결도 모두 끊기게 된다.
  - 그렇기 때문에 테스트 환경에서 사용하는 의존성이 있다면 `@MockBean`으로 만들어서 사용해야 한다.

- 아래는 `@WebMvcTest` 예제 테스트 코드이다.

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(SampleController.class)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Autowired
      MockMvc mockMvc;
  
      @MockBean
      SampleService mockSampleService;
  
      @Test
      public void hello() throws Exception {
          when(mockSampleService.getName()).thenReturn("wooody92");
          mockMvc.perform(get("/hello"))
              .andExpect(content().string("hello wooody92"));
      }
  }
  ```



### @JsonTest

### @WebFluxTest

### @DataJpaTest

<br>

## 테스트 유틸

### OutputCapture

- `logger`와 `System.out.print`로 출력되는 텍스트도 검증 할 수 있다.

  ```java
  @RestController
  public class SampleController {
  
      Logger logger = LoggerFactory.getLogger(SampleController.class);
  
      @Autowired
      private SampleService sampleService;
  
      @GetMapping("/hello")
      public String hello() {
          logger.info("OutputCapture test");
          System.out.println("skip");
          return "hello " + sampleService.getName();
      }
  }
  ```

  ```java
  @RunWith(SpringRunner.class)
  @WebMvcTest(SampleController.class)
  @AutoConfigureMockMvc
  public class SampleControllerTest {
  
      @Rule
      public OutputCaptureRule outputCaptureRule = new OutputCaptureRule();
  
      @Autowired
      MockMvc mockMvc;
  
      @MockBean
      SampleService mockSampleService;
  
      @Test
      public void hello() throws Exception {
          when(mockSampleService.getName()).thenReturn("wooody92");
          mockMvc.perform(get("/hello"))
              .andExpect(content().string("hello wooody92"));
  
          assertThat(outputCaptureRule.toString())
              .contains("OutputCapture test")
              .contains("skip");
      }
  }
  ```

