---
title:  "Spring Boot - 프로파일"
excerpt: "스프링 부트 활용 : @Profile"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Profile
  - spring.profiles.active
  - spring.profiles.include
last_modified_at: 2020-08-29. 15:00:00

---

# 스프링 부트 활용

## 프로파일

### @Profile

- 스프링 프레임워크에서 제공해주며, `@Configuration`, `@Component`와 함께 사용한다.

- 어떤 특정한 프로파일에서만 특정 빈을 등록하고 싶다거나, 애플리케이션 동작을 특정 프로파일에서만 빈 동작을 다르게 하고싶은 경우에 사용한다.

- `@Profile`로 프로파일을 지정할 수 있고, `spring.profiles.active=profile-name`으로 프로파일을 활성화 할 수 있다. 

  ```java
  @Profile("prod")
  @Configuration
  public class BaseConfiguration {
  
      @Bean
      public String hello() {
          return "hello production";
      }
  }
  ```

  ```java
  @Profile("test")
  @Configuration
  public class TestConfiguration {
  
      @Bean
      public String hello() {
          return "hello test";
      }
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      String hello;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println("================");
          System.out.println(hello);
          System.out.println("================");
      }
  }
  ```

  ```java
  // application.properties #1
  spring.profiles.active=prod
    
  // application.properties #1
  spring.profiles.active=test
  ```

  ```java
  // result
  
  // #1
  ================
  hello production
  ================
  
  // #2
  ================
  hello test
  ================
  ```

- `application-{custom}.properties`와 같이 원하는 프로퍼티를 만들 수 있다. 동일한 프로퍼티가 있을 경우에는 `application-{custom}.properties`가 `application.properties`를 오버라이드한다.

- `spring.profiles.include`로 원하는 프로퍼티 파일을 연결할 수 있다.

  ```java
  // application.properties
  henry.name = wooody92
  spring.profiles.include=profile
    
  // application-profile.properties
  henry.name = wooody92 profile
  ```

  ```java
  // result
  
  ================
  wooody92 profile
  ================
  ```

