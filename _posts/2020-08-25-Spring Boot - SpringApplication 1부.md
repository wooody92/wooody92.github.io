---
title:  "Spring Boot - SpringApplication 1부"
excerpt: "스프링 부트 활용 : SpringApplication 실행 방식, 로그 그리고 배너"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - SpringApplication
  - banner
last_modified_at: 2020-08-25. 19:00:00

---

# 스프링 부트 활용

## 스프링 부트 활동 소개

- 스프링 부트 핵심 기능

  ```
  - SpringApplication
  - 외부 설정
  - 프로파일
  - 로깅
  - 테스트
  - Spring-Dev-Tools
  ```

- 각종 기술 연동

  ```
  - Spring Web MVC
  - Spring Data
  - Spring Security
  - REST API 클라이언트
  - 다루지 않은 내용들
  ```



## SpringApplication 1부

### SpringApplication 실행 방식, 로그 그리고 배너

- `SpringApplication` 클래스에 대해 알아보자.

- `SpringApplcation`은 여러 방식으로 실행 할 수 있다. (`static`, `instance`, `builder`)

- 기본 방식인 1번으로 실행하면 스프링 애플리케이션에서 제공하는 다양한 커스텀 기능을 사용하기 어렵다. 그러므로 2, 3번과 같이 활용 할 수있다.

  ```java
  @SpringBootApplication
  public class Application {
  
      /**
       * SpringApplcation 실행 방법
       */
      public static void main(String[] args) {
          // 1 : static method
          SpringApplication.run(Application.class, args);
  
          // 2 : instance
          SpringApplication app = new SpringApplication(Application.class);
          app.run(args);
  
          // 3 : builder
          new SpringApplicationBuilder()
              .sources(Application.class)
              .run(args);
      }
  }
  ```

- 기본적인 애플리케이션의 로그 레벨은 `INFO`이다.

- 로그 레벨을 `DEBUG`로 변경하면 애플리케이션 실행 시 어떤 자동 설정이 적용됐고 어떤 자동설정이 왜 적용 안됐는지도 로그를 통해 확인 할 수 있다.

- 애플리케이션 실행 시 출력되는 배너를 설정할 수 있다.

  - `resources`에 `banner.txt` 추가하여 등록
  - `SpringApplication`에 `setBanner`로 추가 혹은 `setBannerMode`로 배너 사용 `OFF`

