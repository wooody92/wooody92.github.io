---
title:  "Spring Framework 학습정리 - 16"
excerpt: "스프링 AOP : 프록시 기반 AOP"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Spring AOP
  - proxy
  - 프록시 패턴
last_modified_at: 2020-08-12. 19:30:00

---

# Spring AOP

## 스프링 AOP : 프록시 기반 AOP

### Spring AOP 특징

-  `Proxy` 기반의 `AOP` 구현체이고, 스프링 빈에만 `AOP`를 적용할 수 있다.

- 모든 `AOP` 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동하여 엔터프라이즈

  애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적이다.



### 프록시 패턴

- 프록시 패턴은 인터페이스가 있고 클라이언트는 인터페이스 타입으로 프록시 객체를 사용하게 된다.

- 프록시 객체는 같은 타입으로 타겟 객체를 참조하고 있고, 프록시 객체가 타겟 객체를 감싸서 클라이언트 요청을 처리하게 된다.

- 그렇게 사용하는 이유는? 기존의 코드 변경 없이 접근, 제어하고 부가 기능을 추가하기 위해서 사용한다.

  ![proxy AOP](https://user-images.githubusercontent.com/58318041/89997752-88350480-dcc7-11ea-8a5d-db4ed1deed5e.png)

- 위 도식화된 그림을 아래 예시 코드를 통해 학습해보자.

  ```java
  // Subject
  
  public interface EventService {
  
      void createEvent();
  
      void publishEvent();
  
      void deleteEvent();
  
      void crosscuttingConcernsEvent();
  }
  ```

  ```java
  // Real Subject
  
  @Service
  public class SimpleEventService implements EventService{
  
      @Override
      public void createEvent() {
          try {
              Thread.sleep(1000);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println("Created an event");
      }
  
      @Override
      public void publishEvent() {
          try {
              Thread.sleep(2000);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println("Published an event");
      }
  
      @Override
      public void deleteEvent() {
          System.out.println("Deleted an event");
      }
  
      // 아래와 같이 proxy를 이용하지 않고, Real Subject 자체에서 처리하는 방식이 Crosscutting Concerns 이다.
      @Override
      public void crosscuttingConcernsEvent() {
          long begin = System.currentTimeMillis();
          try {
              Thread.sleep(1000);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println("Test event");
          System.out.println(System.currentTimeMillis() - begin);
      }
  }
  ```

  ```java
  // Proxy
  
  @Primary
  @Service
  public class ProxySimpleEventService implements EventService{
  
      @Autowired
      SimpleEventService simpleEventService;
  
      @Override
      public void createEvent() {
          long begin = System.currentTimeMillis();
          simpleEventService.createEvent();
          System.out.println(System.currentTimeMillis() - begin);
      }
  
      @Override
      public void publishEvent() {
          long begin = System.currentTimeMillis();
          simpleEventService.publishEvent();
          System.out.println(System.currentTimeMillis() - begin);
      }
  
      @Override
      public void deleteEvent() {
          simpleEventService.publishEvent();
      }
  
      @Override
      public void crosscuttingConcernsEvent() {
          simpleEventService.crosscuttingConcernsEvent();
      }
  }
  ```

  ```java
  // Client
  
  @Component
  public class AppRunner implements ApplicationRunner {
  
      // 인터페이스가 있는 경우 인터페이스 타입으로 주입받는 것을 권장한다.
      @Autowired
      EventService eventService;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          eventService.createEvent();
          eventService.publishEvent();
          eventService.deleteEvent();
          eventService.crosscuttingConcernsEvent();
      }
  }
  ```

- 동일한 타입의 빈이 여러개 있을 때  `Proxy` 클래스를 우선으로 선택하기위해 `@Primary`를 사용했다.

- 프록시가 하는일은 필요한 부가 기능 추가해서 `delegation(위임)`만 한다.



### 문제점

- 프록시 클래스에서도 중복코드가 발생
- 프록시 클래스를 만드는 비용 : 필요할 때마다 매번 여러 프록시 클래스를 작성해야 하는 문제

### 해결책

- **스프링 AOP**를 사용한다.
- 런타임 중 동적으로 타겟 객체의 프록시 객체를 만들어 처리한다.
  - `스프링 IoC 컨테이너`가 제공하는 기반 시설과 `동적 프록시(Dynamic Proxy)`를 사용하여 위 문제점들을 해결
- `동적 프록시` : 동적으로 프록시 객체를 생성하는 방법이다.
  - 자바가 제공하는 방법은 인터페이스 기반 프록시 생성
- `스프링 IoC` : 기존 빈을 대체하는 동적 프록시 빈을 만들어 등록 시켜준다.
  - 클라이언트 코드 변경 없음
  - AbstractAutoProxyCreator​ implements ​BeanPostProcessor



### 스프링 AOP가 어떻게 적용될까?

- 타겟 객체(`real subject`, 위 예제 코드에서는 `SimpleEventService` 클래스)가 빈으로 등록이 되면, 스프링이 `AbstractAutoProxyCreator`를 이용하여 타겟 객체를 감싸는 프록시 빈을 만들고, 그 프록시 빈을 타객 객체 대신 빈으로 등록을 해준다.

