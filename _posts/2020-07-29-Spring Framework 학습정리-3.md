---
title:  "Spring Framework 학습정리 - 3"
excerpt: "IoC 컨테이너 2부 : @Autowire"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - @Autowired
  - BeanPostProcessor
last_modified_at: 2020-07-29 15:50:00

---

# IoC 컨테이너와 빈

## Ioc 컨테이너 3부 : @Autowire

### @Autowired

- 필요한 의존 객체의 타입에 해당하는 빈을 찾아 주입한다.

  ```java
  // default는 true이며, 빈을 못찾으면 애플리케이션 구동에 실패한다.
  @Autowired(required = false)
  ```

- `생성자`를 사용한 의존성 주입은 `BookService` 빈 생성시 `BookReository` 빈을 찾지 못하면 생성되지 않는다.

- `Setter`와 `필드` 의존성 주입 방식의 경우는 `required = false (optional)`로 설정하여 `BookRepository`의 의존성 없이도 `BookService` 빈만 생성 할 수 있다.

  ```java
  @Service
  public class BookService {
     // 생성자
     private BookRepository bookRepository;
  
     @Autowired
     public BookService(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
     }
    
     // Setter
     @Autowired(required = false)
     public void setBookRepository(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
     }
    
     // 필드
     @Autowired(required = false)
     private BookRepository bookRepository;
  }
  ```



### 같은 타입의 빈이 여러개 일 때

- `BookRepository`가 `interface`이고 아래와 같이 구현되었을 때 `@Primary`가 붙은 타입을 우선하여 가져온다. (권장)

- `List<BookRepository>`로 모두 주입 받을 수 있다.

- `@Qualifier("henryBookRepository")`로 주입 받을 수 있다.

  ```java
  @Service
  public class BookService {
     @Autowired
     private BookRepository bookRepository;
  }
  
  @Repository
  public interface BookRepository {
  }
  
  @Repository
  @Primary
  public class HenryBookRepository implements BookRepository{
  }
  
  @Repository
  public class WooodyBookRepository implements BookRepository{
  }
  ```



### 동작 원리

- `BeanPostProcessor` : 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스

- 빈을 만들고 빈을 초기화하는(Initial) 라이프 사이클 전, 후에 또 다른 작업을 할 수 있는 라이프사이클 콜백

- 빈 팩토리(`ApplicationContext`)가 등록이 되어있는 `BeanPostProcessor` 타입의 빈을 찾고 다른 일반적인 빈들에 `BeanPostProcessor`의 로직을 적용한다.

- ```java
  @PostConstruct
  public void postConstruct() {
    System.out.println("Hello");
  }
  ```

