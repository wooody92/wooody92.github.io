---
title:  "Spring Framework 학습정리 - 2"
excerpt: "IoC 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - ApplicationContext
last_modified_at: 2020-07-28 20:20:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법

### 스프링 빈 설정파일 만들기

- 기존의 `xml`파일을 이용한 빈 등록방식은 복잡하고 사용자 편의성이 좋지 않았다.
- 그래서 등장한 것이 `@ComponentScan`이다. (`@Annotation` 사용)

### Java 설정파일로 빈 등록 및 의존성 주입 방법

- `@Component (@Service, @Repository 등)` 활용

- `Configuration Class` 생성 후 하나하나 빈으로 등록 후 엮기

  ```java
  @Configuration
  public class ApplicationConfig {
  
      @Bean
      public BookRepository bookRepository() {
          return new BookRepository();
      }
  
      @Bean
      public BookService bookService(BookRepository bookRepository) {
          BookService bookService = new BookService();
          bookService.setBookRepository(bookRepository);
          return bookService;
      }
  }
  ```

- 위 `BookRepository`와 같이 빈으로만 등록 후 `BookService` 클래스에서 `@Autowired` 로 엮을 수 있다.

- `@ComponentScan` 이용

  ```java
  @Configuration
  @ComponentScan(basePackageClasses = SpringStudyApplication.class)
  public class ApplicationConfig {
      
  }
  ```

- `MainApplicationClass`에 `@SpringBootApplication` 사용 -> 내부에 `Configuration & ComponentScan` 포함하고 있다.

  - `Configuration & ComponentScan`은 `SpringApplicationContext`에 관한 내용이다.

