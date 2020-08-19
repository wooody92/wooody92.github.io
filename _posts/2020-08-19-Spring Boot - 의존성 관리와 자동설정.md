---
title:  "Spring Boot - 의존성 관리와 자동설정"
excerpt: "스프링 부트 원리 : 의존성 관리와 자동 설정 이해"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - 의존성 관리
  - SpringBootApplication
  - EnableAutoConfiguration
  - ComponentScan
last_modified_at: 2020-08-19. 21:00:00

---

# 스프링 부트 원리

## 의존성 관리 이해와 응용

### 의존성 관리 (Dependancy Management)

- 스프링 부트에서 의존성 관리를 해주기 때문에, 우리가 관리해야 할 의존성의 수가 줄어 관리가 편한 장점이 있다.

- 아래와 같이 간단히 `spring-boot-starter-web` 만 추가해도 `parent`에서 기본적인 외부 라이브러리(의존성) 등이 추가되어 사용 할 수 있다.

- `parent`에서 많은 `Java 1.8 default complie`, `UTF-8 Encoding` 등 많은 기본 설정을 지원해준다. 

- `Spring boot dependancy`에서 관리하는 의존성을 추가할때는 특별히 버전을 작성해주지 않아도 되지만, 원하는 버전을 명시적으로 작성하는 것이 좋다.

  ```java
    <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.3.3.RELEASE</version>
    </parent>
  
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
    </dependencies>
  ```

### 의존성 추가 및 버전 변경하기

- https://mvnrepository.com/ 에서 원하는 의존성을 찾아 추가 할 수 있다.
- `parent` 상위 객체에서 설정되어 있는 스프링 혹은 자바 버전을 변경할 수 있다.



## 자동 설정 이해

### SpringBootApplication

- `@SpringBootApplication`은 빈을 두번 등록한다. (두 단계로 나눠서 읽힌다.)

  -  `1단계` : `@ComponentScan`으로 빈을 등록
  - `2단계` : `@EnableAutoConfiguration`으로 읽어온 빈들을 다시 등록

  ```java
  @SpringBootApplication
  
  // @SpringBootApplication 내부에 아래 어노테이션들이 구현되어 있다.
  @SpringBootConfiguration
  @ComponentScan
  @EnableAutoConfiguration
  ```

### EnableAutoConfiguration

- `default`로 웹 애플리케이션을 만드려 하기 때문에 `@EnableAutoConfiguration`이 없으면 `ServletWebServerFacotry`의 빈을 찾지 못해 에러가 발생한다.

  - `ServletWebServerFacotry`의 빈은 `@EnableAutoConfiguration`에서 자동으로 만들어 주는 빈이다.

  ```java
  @SpringBootConfiguration
  @ComponentScan
  //@EnableAutoConfiguration
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication.run(Application.class, args);
      }
  }
  ```

  ```java
  // result
  org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.context.ApplicationContextException: Unable to start ServletWebServerApplicationContext due to missing ServletWebServerFactory bean.
  ```

- 아래와 같이 `@EnableAutoConfiguration` 설정을 지우고 웹 애플리케이션이 아닌 일반 애플리케이션으로 만들 수도 있다. (웹 서버로는 동작하지 않는다.)

  ```java
  @SpringBootConfiguration
  @ComponentScan
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication application = new SpringApplication(Application.class);
          application.setWebApplicationType(WebApplicationType.NONE);
          application.run(args);
      }
  }
  ```



### @ComponentScan

- 현재 패키지로부터(`@ComponentScan`이 동작하는) 하위 패키지까지 `@Component`를 가진 클래스들을 스캔 후 빈으로 등록한다.
- `@Configuration`, `@Repository`, `@Service`, `@Controller`, `@RestController`

### @EnableAutoConfiguration

- `External Libraries` -> `org.springframework.boot.autoconfigure.EnableAutoConfiguration` -> `META-INF` -> `spring.factories`에 있는 자바 설정 파일들을 읽어온다.

  - `AutoConfiguration` 으로 읽어오는 설정 파일들을 들어가보면 `Configuration`으로 정의되어있다.

  - 한번에 전부 불러오는 것이 아니고 조건부로 읽어온다.

    ```java
    // 아래의 조건들이 맞을때만 빈 등록을 시작한다.
    @Configuration(proxyBeanMethods = false)
    @ConditionalOnWebApplication(type = Type.SERVLET)
    @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
    @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
    ```

- <img width="1098" alt="autoConfig" src="https://user-images.githubusercontent.com/58318041/90632239-10368380-e25f-11ea-846c-ff210c94b4d5.png">

