---
title:  "Spring Framework 학습정리 - 8"
excerpt: "Ioc 컨테이너 8부 : ApplicationEventPublisher"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - ApplicationEventPublisher
  - POJO
last_modified_at: 2020-08-04 17:00:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 8부 : ApplicationEventPublisher

### ApplicationEventPublisher

- 옵저버 패턴의 구현체로 이벤트 기반의 프로그래밍에 필요한 인터페이스이다.

- 이벤트는 빈이 아니고 원하는 데이터를 담아 전송할 수있는 `POJO` 객체이다

  ```java
  public class MyEvent {
  
      private int data;
      private Object source;
  
      public MyEvent(Object source, int data) {
          this.source = source;
          this.data = data;
      }
  
      public int getData() {
          return data;
      }
  
      public Object getSource() {
          return source;
      }
  }
  ```

- 이벤트 핸들러는 빈으로 등록되어야 하고, 이벤트 핸들러가 여러개인 경우 동일한 쓰레드에서 어느것이 먼저 처리되는지는 모르지만 순차적으로 처리된다.

  ```java
  @Component
  public class MyEventHandler {
  
      @EventListener
      public void handle(MyEvent event) {
          System.out.println(Thread.currentThread().toString());
          System.out.println("Event #1. Data is " +event.getData());
      }
  }
  
  @Component
  public class AnotherHandler {
  
      @EventListener
      public void handle(MyEvent event) {
          System.out.println(Thread.currentThread().toString());
          System.out.println("Event #2. Data is " +event.getData());
      }
  }
  ```

- 이 이벤트를 발생시키는 클래스를 `ApplicationContext - ApplicationEventPublisher`가 갖고있다.

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      ApplicationEventPublisher publisherEvent;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          publisherEvent.publishEvent(new MyEvent(this, 100));
      }
  }
  ```

  ```java
  // result
  Thread[restartedMain,5,main]
  Event #2. Data is 100
  Thread[restartedMain,5,main]
  Event #1. Data is 100
  ```

- `Order`를 이용하여 이벤트 핸들러에 순서 줄 수 있고, `Async`와 `EnableAsync`를 이용하여 이베트 핸들러를 비동기 처리 할 수 있다.

  ```java
  // order
  @Component
  public class MyEventHandler {
  
      @EventListener
      @Order(Ordered.HIGHEST_PRECEDENCE)
      public void handle(MyEvent event) {
          System.out.println(Thread.currentThread().toString());
          System.out.println("Event #1. Data is " +event.getData());
      }
  }
  
  // async
  @Component
  public class MyEventHandler {
  
      @EventListener
      @Async
      public void handle(MyEvent event) {
          System.out.println(Thread.currentThread().toString());
          System.out.println("Event #1. Data is " +event.getData());
      }
  }
  
  @SpringBootApplication
  @EnableAsync
  public class SpringStudyApplication {
  
  	public static void main(String[] args) {
  		SpringApplication.run(SpringStudyApplication.class, args);
  	}
  
  }
  ```

  ```java
  // asynce result
  // 다른 쓰레드에서 동작한다.
  Thread[task-4,5,main]
  Thread[task-3,5,main]
  Event #1. Data is 100
  Event #2. Data is 100
  ```



### POJO

- `Plain Old Java Object`
- 특정 기술에 종속되어 동작하는 것이 아닌 순수한 자바 객체를 말한다.
- 자바 객체가 특정 기술과 환경에 종속되어 의존하게 되면, 가독성이 떨어지고 유지보수가 힘들며 확장성 또한 떨어지는 등 객체지향 설계의 장점들을 잃어버린다.
- 진정한 `POJO`란 객체지향적인 원리에 충실하면서, 환경과 기술에 종속되지 않고 필요에 따라 재활용될 수 있는 방식으로 설계된 오브젝트를 말한다. (토비의 스프링)

### Spring의 철학

- 스프링 패키지가 전혀 들어있지 않는 `POJO` 기반의 프로그래밍. 즉, 스프링 프레임워크의 코드가 나의 코드에 노출되지 않는 `POJO` 기반의 프로그래밍을 말한다.
- 이는 테스트와 유지보수에 큰 장점이 있다. 
- `비침투성`, `Transparent`

