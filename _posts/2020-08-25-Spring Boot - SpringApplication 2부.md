---
title:  "Spring Boot - SpringApplication 2부"
excerpt: "스프링 부트 활용 : EventListenr, WebApplicationType 그리고 arguments"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - SpringApplication
  - EventListenr
  - WebApplicationType
  - Arguments
last_modified_at: 2020-08-25. 19:20:00

---

# 스프링 부트 활용

## SpringApplication 2부

### ApplicationContext 생성 시점으로 보는 EventListenr

- `Application Event`는 애플리케이션이 시작 할 때, 컨텍스트가 등록 됐을 때, 종료 될 때 등 다양한 시점이 있다.

- 이벤트 리스너의 간단한 동작 방식은 다음과 같다.

  ```java
  1. 인터페이스로 상속 받은 ApplicationListener<Event>의 이벤트가 특정 시점에 발생
  2. 이벤트 리스너가 EventType의 이벤트가 발생하면 생성한 리스너가 실행
  ```

- 일반적인 경우 생성한 리스너 클래스를 `Component`로 등록을 하고 `ApplicationListener<Event>`에서 설정한 이벤트가 발생하면 빈으로 등록된 해당 리스너를 찾아 실행한다.

- 여기서 주의 할 점은 `ApplicationContext`가 만들어지는 기점으로 빈의 등록여부가 결정되며 그로 인해 리스너 빈을 찾을 수 있는지 여부가 구분된다.

  - `ApplicationContext`가 생성된 이후라면 리스너가 컴포넌트로 등록되어 이벤트 발생 시 자동으로 빈을 찾아 호출 할 수 있다.
  - `ApplicationContext`가 생성되기 전이라면 빈을 찾을 수 없어서 실행이 안되므로 직접 등록을 해줘야 한다.

- `Application` 시작 시점에 이벤트 리스너가 호출된다. (`ApplicationContext` 생성 시점 이 전)

  ```java
  //@Component
  public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {
  
      @Override
      public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
          System.out.println("=======================");
          System.out.println("Application is Starting");
          System.out.println("=======================");
      }
  }
  ```

  ```java
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.addListeners(new SampleListener());
          app.run(args);
      }
  }
  ```

  ```java
  // result
  
  =======================
  Application is Starting
  =======================
  =======================
  =  Henry Spring Boot  =
  =  2.3.3.RELEASE =
  =======================
  2020-08-25 18:28:25.914  INFO 29315 --- [           main] dev.springboot.study.Application         : Starting Application on yungyeocBookPro with PID 29315 (/Users/henry/Documents/study/spring-practice/spring-boot-study/target/classes started by henry in /Users/henry/Documents/study/spring-practice/spring-boot-study)
  ```

- `Application` 시작된 후 시점에 이벤트 리스너가 호출된다. (`ApplicationContext` 생성 시점 이 후)

  ```java
  @Component
  public class SampleListener implements ApplicationListener<ApplicationStartedEvent> {
  
      @Override
      public void onApplicationEvent(ApplicationStartedEvent applicationStartedEvent) {
          System.out.println("=======================");
          System.out.println("Application is Started");
          System.out.println("=======================");
      }
  }
  ```

  ```java
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.run(args);
      }
  }
  ```

  ```java
  // result
  
  2020-08-25 18:29:28.637  INFO 29322 --- [           main] dev.springboot.study.Application         : Started Application in 1.056 seconds (JVM running for 1.464)
  =======================
  Application is Started
  =======================
  ```



### WebApplicationType 설정

- 기본적으로 `Servlet Web MVC`가 있으면 `WebApplicationType.SERVLET`으로 실행된다.

- 서블릿이 없고 `Spring WebFlux`가 있으면 `WebApplicationType.REACTIVE`로 실행된다.

- 서블릿과 웹 플럭스 둘다 들어있다면 서블릿이 우선순위로 동작한다. 그러나 아래와 같은 타입 설정으로 웹 플럭스를 사용 할 수 있다.

  ```java
  @SpringBootApplication
  public class Application {
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.setWebApplicationType(WebApplicationType.REACTIVE);
          app.run(args);
      }
  }
  ```



### Application Arguments

- 콘솔로 들어 올때 `-D`로 시작하는 것은 `JVM option`이고, `--`로 시작하는 것을 `application arguments`이다.

- `JVM option`은 `application arguments`가 아니고, `--bar`와 같이 시작하는 것이 `application arguments`이다.

- `arguments`에는 여러 메서드가 있으며 필요에 따라 사용하면 된다.

  <img width="1060" alt="arguments" src="https://user-images.githubusercontent.com/58318041/91161114-45cae900-e705-11ea-90e0-bbcb30662d9b.png">



### Application 실행 후 기능을 실행하고 싶을 떄

- `ApplicationRunner`와 `CommandLineRunner` 두 가지 방법이 있는데 `ApplicationRunner` 사용을 권장한다.

- `JVM option`은 받지 못하고 `arguments`만 받을 수 있다.

- `ApplicationRunner`가 여러개라면 `@Order`로 순서를 지정 할 수 있다

  ```java
  @Component
  //@Order(1)
  public class TestListener implements ApplicationRunner {
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println("debug : " + args.containsOption("debug"));
          System.out.println("bar : " + args.containsOption("bar"));
      }
  }
  ```

  ```java
  // result
  
  debug : false
  bar : true
  ```

