---
title:  "Spring Boot - 외부 설정 2부"
excerpt: "스프링 부트 활용 : @ConfigurationProperties"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - property
  - ConfigurationProperties
  - application.properties
last_modified_at: 2020-08-28. 17:20:00

---

# 스프링 부트 활용

## 외부 설정 2부

### @ConfigurationProperties

- `application.properties`에 동일한 `key`의 프로퍼티가 많을 경우 묶어서 하나의 빈으로 등록 할 수 있다.

- `@ConfigurationProperties("key")`는 해당 클래스가 어떤 `key`에 대하여 바인딩하는지 결정한다.

- 원래는 `EnableConfigurationProperties(MyProperties.class)`로 등록해줘야 하나 기본적으로 자동 등록되도록 되어있다.

- 그러므로 `@Component`를 이용하여 외부 프로퍼티와 바인딩되는 클래스만 빈으로 등록해주면 된다.

- 기존에 `@Value`를 이용했던 방식과 다르게 `@Autowired`로 주입받고 좀 더 타입 세이프하게 클래스로 관리 할 수 있다. (`@Value`는 `SpEL`을 사용할 수 있지만 다른 기능들은 사용 못한다.)

  ```java
  // application.properties
  
  henry.name = wooody92
  henry.age = ${random.int(0,100)}
  henry.fullName = ${henry.name} Yoon
  ```

  ```java
  @Component
  @ConfigurationProperties("henry")
  public class HenryProperties {
  
      private String name;
  
      //  `Spring Conversion Service` 통해서 자동으로 `Type Conversion`이 일어난다.
      private int age;
  
      private String fullName;
  
      public void setName(String name) {
          this.name = name;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  
      public void setFullName(String fullName) {
          this.fullName = fullName;
      }
  
      public String getName() {
          return name;
      }
  
      public int getAge() {
          return age;
      }
  
      public String getFullName() {
          return fullName;
      }
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      HenryProperties henryProperties;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println("================");
          System.out.println(henryProperties.getFullName());
          System.out.println(henryProperties.getAge());
          System.out.println("================");
      }
  }
  ```

  ```java
  @SpringBootApplication
  //@EnableConfigurationProperties(HenryProperties.class)
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.setWebApplicationType(WebApplicationType.NONE);
          app.run(args);
      }
  }
  ```

- 아래 의존성은 자동완성에 필요한 메타정보를 생성해주는 플러그인이다. [Spring Docs](https://docs.spring.io/spring-boot/docs/2.3.3.RELEASE/reference/html/appendix-configuration-metadata.html#configuration-metadata)

  ```java
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```

<br>

### 융통성있는 바인딩

- `application.properties`에서 `camel, kebab, snake case` 동일하게 바인딩 해준다.

  ```java
  henry.fullName = ${henry.name} Yoon
  henry.full-name = ${henry.name} Yoon
  henry.full_name = ${henry.name} Yoon
  ```

<br>

### 프로퍼티 타입 컨버전

- `application.properties` 문서 안에서는 자료형 타입이 없고 전부 문자열이다.
- 스프링 프레임워크가 제공하는 `Conversion Service` 통해서 `Type Conversion`이 일어난다. (`age` 프로퍼티 값이 문자열에서 정수형으로 자동 변환)
- `@DurationUnit` : 시간 관련 타입

<br>

### 프로퍼티 값 검증

- `@Validated`를 이용하여 바인딩되는 프로퍼티 값을 검증 할 수 있다.

