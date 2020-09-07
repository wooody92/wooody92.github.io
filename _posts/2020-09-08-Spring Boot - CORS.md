---
title:  "Spring Boot - CORS"
excerpt: "스프링 Web MVC : CORS, SOP"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Web MVC
  - CORS
  - SOP
  - CrossOrigin
last_modified_at: 2020-09-08. 00:10:00

---

<br>

# 스프링 부트 활용

## Spring Web MVC 11부 : CORS

> CORS는 서로 다른 오리진끼리 리소스를 공유할 수 있는 방법을 제공하는 표준이며, SOP에서는 같은 오리진끼리만 요청과 응답을 주고 받을 수 있다.

### CORS (Cross Orgin Resource Sharing)

- `SOP`를 우회하기 위한 웹 브라우저가 지원하는 표준 기술이다.
- 기본적으로 `SOP`가 적용되어 있어서 하나의 오리진이 또 다른 오리진을 호출 할 수 없다.
- 예를들어 `REST API`를 제공하는 서버(`localhost:8080`)와 그 서버의 리소스를 사용하기 위한 클라이언트(`localhost:18080`)는 서로 다른 오리진(여기서는 `port`)이기 때문에 `SOP`에 위반되어 클라이언트에서 요청시 `CORS error`가 발생한다.
- 이를 우회적으로 처리하기 위해 `CORS` 설정을 해야한다.
- 그렇다면 `SOP`와 `Origin`은 무엇일까? 

### SOP (Single Origin Policy)

- `SOP`는 같은 오리진에서만 요청을 보낼 수 있도록 만든 정책이다.
- 웹 브라우저 보안을 위해 프로토콜, 호스트, 포트가 동일한 서버로만 리소스 요청을 주고 받을 수 있도록 한 정책이다.
- `Origin`이란? 아래 세가지를 조합한 것 이다. (프로토콜, 호스트, 포트) 
  - URI 스키마 (`http`, `https`)
  - hostname (`henry.me`, `localhost`)
  - 포트 (`8080`, `18080`)

### CORS 설정 방법

- `CORS`는 스프링 MVC가 지원하며, 스프링 부트는 기능을 사용하기 위한 빈 설정을 자동으로 해준다.

- 그래서 `@CrossOrigin`을 별다른 설정없이 사용 가능하다.

- `CORS` 설정을 하지 않고 다른 오리진에서 요청하면 `CORS` 에러가 발생한다. 이를 해결하기 위해 서버 단에서 어떠한 오리진이 서버에 접근할 수 있는지 관련 헤더를 클라이언트에게 보내줘야 한다.

- 방법 1 : 메서드 또는 컨트롤러 단에 `@CrossOrigin`을 설정하여 지역적으로 설정할 수 있다.

  ```java
  @SpringBootApplication
  @RestController
  public class Application {
  
      @CrossOrigin(origins = "http://localhost:18080")
      @GetMapping("/cors")
      public String corsTest() {
          return "hello";
      }
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.run(args);
      }
  }
  ```

- 방법 2 : `WebConfig` 클래스를 생성하여 `@Configuration` 설정을 통해 글로벌로 설정 할 수 있다.

  -  `WebMvcConfigurer`를 `implements`하여 스프링 MVC 기능을 그대로 사용하며 확장만 할 수 있다.

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
  
      @Override
      public void addCorsMappings(CorsRegistry registry) {
          registry.addMapping("/cors")
              .allowedOrigins("http://localhost:18080");
      }
  }
  ```

