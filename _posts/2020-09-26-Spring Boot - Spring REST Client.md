---
title:  "Spring Boot - Spring REST Client"
excerpt: "스프링 REST Client : RestTemplate과 WebClient"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring REST Client
  - RestTemplate
  - WebClient
last_modified_at: 2020-09-26. 13:20:00

---

<br>

# 스프링 부트 활용

## Spring REST 클라이언트 1부 : RestTemplate과 WebClient

> RestTemplate과 WebClient의 차이에 대해 알아보자.

### 들어가기 전에

- RestTemplate
  - Synchronous & Blocking
  - web 요청을 하고 다음 행동을 하려면 응답이 올때까지 기다려야한다.
- WebClient
  - Asynchronous & Non-Blocking
  - web 요청을 하고 기다리지 않고 요청의 응답이 끝나면 콜백을 받아 처리한다.
  - `block()` 설정으로 Synchronous 하게 사용 할 수도 있다.

### 개요

- `REST Client`는 스프링 프레임워크에서 제공하는 것이고, 스프링 부트는 `REST Client`를 쉽게 사용할 수 있도록 빈을 등록해준다.

- 여기서 주의할 점은 스프링 부트가 등록해주는 빈이 `RestTemplate`, `WebClient` 두 타입의 빈이 아니다.

  - 프로젝트에 spring-web 모듈이 있다면 `RestTemplateBuilder`를 빈으로 등록
  - 프로젝트에 spring-webflux 모듈이 있다면 `WebClient.Builder`를 빈으로 등록

- 우리는 `Builder`를 주입받고 필요시마다 `build` 해서 `REST Client`의  인스턴스를 생성해서 사용해야 한다.

- 아래는 예제로 사용 될 Controller이다. 각 요청에 5초, 3초의 딜레이는 갖는다.

  ```java
  @RestController
  public class SampleController {
  
      @GetMapping("/hello")
      public String hello() throws InterruptedException {
          Thread.sleep(5000);
          return "hello";
      }
  
      @GetMapping("/world")
      public String world() throws InterruptedException {
          Thread.sleep(3000);
          return "world";
      }
  }
  ```

  

### RestTemplate

- `RestTemplate`는 Blocking I/O 기반의 Synchronous(동기) API이다.

- 그렇기 때문에 각 `/hello, /world` HTTP 요청시 응답이 올떄까지 기다리고 다음 요청을 보낸다. 결과적으로 (5초 + 3초)가 걸리는 것을 볼 수 있다.

- `RestTemplateAutoConfiguration`을 확인해 보면 `restTemplateBuilder`가 빈으로 등록되어 있는 것을 확인 할 수 있다.

- 그렇기 때문에 `restTemplateBuilder`를 주입받아 사용할 수 있다.

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      RestTemplateBuilder restTemplateBuilder;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          RestTemplate restTemplate = restTemplateBuilder.build();
  
          StopWatch stopWatch = new StopWatch();
          stopWatch.start();
  
          String helloResult = restTemplate.getForObject("http://localhost:8080/hello", String.class);
          System.out.println(helloResult);
  
          String worldResult = restTemplate.getForObject("http://localhost:8080/world", String.class);
          System.out.println(worldResult);
  
          stopWatch.stop();
          System.out.println(stopWatch.prettyPrint());
      }
  }
  ```

  ```java
  // result
  hello
  world
  StopWatch '': running time = 8101695519 ns
  ---------------------------------------------
  ns         %     Task name
  ---------------------------------------------
  8101695519  100% 
  ```

  

### WebClient

- `WebClient`는 Non-Blocking I/O 기반의 Asynchronous(비동기) API이다.

- 각 `/hello, /world` HTTP 요청하고 응답이 올떄까지 기다리않고 다음 요청을 보낸다.

- 3초 후에 `/world`의 응답이 오고, 추가로 2초 뒤에(총 5초) `/hello`의 응답을 받는다. 결과적으로 총 5초가 걸리는 것을 볼 수 있다.

- `WebClientAutoConfiguration`을 확인해 보면 `WebClient.Builder`가 빈으로 등록되어 있는 것을 확인 할 수 있다.

- 같은 방식으로 `WebClient.Builder`를 주입받아 사용할 수 있다.

- `Mono`와 같은 straming api는 `subscribe` 하기 전까지 흐르지 않는다.

- `WebClient` 사용을 위해 `webflux` 의존성을 추가한다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
  </dependency>
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      WebClient.Builder builder;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          WebClient webClient = builder.baseUrl("http://localhost:8080").build();
  
          StopWatch stopWatch = new StopWatch();
          stopWatch.start();
  
          Mono<String> helloMono = webClient.get().uri("/hello")
              .retrieve()
              .bodyToMono(String.class);
          helloMono.subscribe(s -> {
              System.out.println(s);
              if (stopWatch.isRunning()) {
                  stopWatch.stop();
              }
              System.out.println(stopWatch.prettyPrint());
              stopWatch.start();
          });
  
          Mono<String> worldMono = webClient.get().uri("/world")
              .retrieve()
              .bodyToMono(String.class);
          worldMono.subscribe(s -> {
              System.out.println(s);
              if (stopWatch.isRunning()) {
                  stopWatch.stop();
              }
              System.out.println(stopWatch.prettyPrint());
              stopWatch.start();
          });
      }
  }
  ```

  ```java
  // result
  world
  StopWatch '': running time = 3367715208 ns
  ---------------------------------------------
  ns         %     Task name
  ---------------------------------------------
  3367715208  100%  
  
  hello
  StopWatch '': running time = 5326503674 ns
  ---------------------------------------------
  ns         %     Task name
  ---------------------------------------------
  3367715208  063%  
  1958788466  037% 
  ```

<br>

## Spring REST 클라이언트 2부 : 커스터마이징

### 개요

- `RestTemplateBuilder`와 `WebClient.Builder`를 커스터마이징하여 사용 할 수 있다. (하지않고 사용해도 아무 지장없다.)
- RestTemplate
  - 기본으로 `java.net.HttpURLConnection` 사용한다.
  - `RestTemplateCustomizer` 인터페이스를 빈으로 재정의하여 글로벌 커스터마이징 할 수 있다.
- WebClient
  - 기본으로 `Reactor Netty`의 `HTTP 클라이언트` 사용한다.
  - `WebClientCustomizer` 인터페이스를 빈으로 재정의하여 글로벌 커스터마이징 할 수 있다.



### Class 레벨에서 webClient 사용하기 - 로컬

- 생성자로 주입받아 클래스 내부에서만 유효한 `webClient`를 사용 할 수 있다.

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
      
      WebClient webClient;
      
      public SampleRunner(WebClient.Builder builder) {
          this.webClient = builder.build();
      }
  }
  ```

  

### webClientBuilder 전역적으로 설정하기 - 글로벌

- `WebClientCustomizer` 인터페이스를 빈으로 설정하여 모든 `webClientBuilder`가 `baseUrl`이 설정 된 상태로 다른 빈들에게 주입 되도록 할 수 있다.

  ```java
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.run(args);
      }
  
      @Bean
      public WebClientCustomizer webClientCustomizer() {
          return webClientBuilder -> webClientBuilder.baseUrl("http://localhost:8080");
      }
  }
  ```



### Apache HttpTemplate로 변경해서 사용하기 - 글로벌

- 의존성을 추가한다.

  ```xml
  <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpclient</artifactId>
      <version>4.5.12</version>
  </dependency>
  ```

- `new HttpComponentsClientHttpRequestFactory()`를 추가하면 밑단에서 더 이상 Java의 기본 `java.net.HttpURLConnection`을 사용하지 않고, `Apache HttpClient`를 사용한다.

  ```java
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.run(args);
      }
  
      @Bean
      public RestTemplateCustomizer restTemplateCustomizer() {
          return new RestTemplateCustomizer() {
              @Override
              public void customize(RestTemplate restTemplate) {
                  restTemplate.setRequestFactory(new HttpComponentsClientHttpRequestFactory());
              }
          };
      }
  }
  ```

  

## References

- 스프링 부트 개념과 활용 - 백기선 님
- [https://www.baeldung.com/spring-webclient-resttemplate](https://www.baeldung.com/spring-webclient-resttemplate)
- [https://juneyr.dev/2019-02-12/resttemplate-vs-webclient](https://juneyr.dev/2019-02-12/resttemplate-vs-webclient)

