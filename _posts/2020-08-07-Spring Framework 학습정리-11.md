---
title:  "Spring Framework 학습정리 - 11"
excerpt: "Validation 추상화"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - Validation
  - Validator
  - Bean Validation
  - LocalValidatorFactoryBean
last_modified_at: 2020-08-07 13:00:00

---

# Resource / Validation

## Validation 추상화

### Validator

- 애플리케이션에서 사용하는 객체를 검증할 때 사용하는 인터페이스이다.
- 주로 `Spring MVC`에서 사용하지만 웹계층만을 위한 `Validator`는 아니고 모든 계층에서(`Web`, `Service`, `Data`) 사용해도 좋다.
- `BeanValidation` : 자바 표준 스펙이고 `@NotEmpty`, `@NotNull`, `@Size`, `@Email` 등 어노테이션을 이용하여  빈에 있는 데이터를 검증하는 기능이다.



### 인터페이스

- `boolean supports(Class clazz)` : 어떤 타입의 객체를 검증할 때 사용할 것인지 결정한다.

- `void validate(Object obj, Errors e)` : 실제 검증 로직을 이 안에서 구현한다. 구현할 때 `ValidationUtils` 사용하며 편리하다.

- 아래와 같이 실제로 구현하는 방식은 예전 방식으로 잘 사용하지 않고, 스프링에서 기본적으로 제공하는 `BeanValidation` 어노테이션들을 사용한다. 하지만 기본 어노테이션 외에 복잡한 `Validation` 검증 시 사용 할 수 있다.

  ```java
  public class EventValidator implements Validator {
  
      @Override
      public boolean supports(Class<?> clazz) {
          return Event.class.equals(clazz);
      }
  
      @Override
      public void validate(Object target, Errors errors) {
          ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title", "notEmpty", "Empty title is not allowed");
      }
  }
  ```

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Event event = new Event();
          EventValidator eventValidator = new EventValidator();
          Errors errors = new BeanPropertyBindingResult(event, "event");
  
          eventValidator.validate(event, errors);
  
          System.out.println(errors.hasErrors());
  
          errors.getAllErrors().forEach(e -> {
              System.out.println("===== error code =====");
              Arrays.stream(e.getCodes()).forEach(System.out::println);
              System.out.println(e.getDefaultMessage());
          });
      }
  }
  ```

  ```java
  // result
  true
  ===== error code =====
  notEmpty.event.title
  notEmpty.title
  notEmpty.java.lang.String
  notEmpty
  Empty title is not allowed
  ```



### 스프링 부트 2.0.5 이상 버전을 사용 할 때

- `LocalValidatorFactoryBean`를 빈으로 자동으로 등록해 준다. 

  - `BeanValidation` 애노테이션 들을 지원하는 `Validator`이다.

- `JSR-380(Bean Validation 2.0.1`) 구현체로 `hibernate-validator` 사용.

- http://hibernate.org/validator/

  ```java
  @Getter @Setter
  public class Event {
  
      Integer id;
  
      @NotEmpty
      String title;
  
      @Min(0)
      Integer limit;
  
      @Email
      String email;
  }
  ```

  ```java
  @Component
  public class AppRunner implements ApplicationRunner {
  
      @Autowired
      Validator validator;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println(validator.getClass());
  
          // 테스트를 위한 유효하지 않는 값 입력
          Event event = new Event();
          event.setLimit(-1);
          event.setEmail("abcde");
          Errors errors = new BeanPropertyBindingResult(event, "event");
  
          validator.validate(event, errors);
          System.out.println(errors.hasErrors());
  
          errors.getAllErrors().forEach(e -> {
              System.out.println("===== error code =====");
              Arrays.stream(e.getCodes()).forEach(System.out::println);
              System.out.println(e.getDefaultMessage());
          });
      }
  }
  ```

  ```java
  // result
  class org.springframework.validation.beanvalidation.LocalValidatorFactoryBean
    
  true
  ===== error code =====
  Min.event.limit
  Min.limit
  Min.java.lang.Integer
  Min
  0 이상이어야 합니다
  ===== error code =====
  Email.event.email
  Email.email
  Email.java.lang.String
  Email
  올바른 형식의 이메일 주소여야 합니다
  ===== error code =====
  NotEmpty.event.title
  NotEmpty.title
  NotEmpty.java.lang.String
  NotEmpty
  비어 있을 수 없습니다
  ```



### 기타

- 내 경우에는 토이 프로젝트 진행 시 아래 의존성을 `build.gradle`에 추가했고, `Validation` 어노테이션들을 사용하여 `Web` 계층의 객체의 유효성을 검증하였다.

  ```java
  implementation 'org.springframework.boot:spring-boot-starter-validation'
  ```

