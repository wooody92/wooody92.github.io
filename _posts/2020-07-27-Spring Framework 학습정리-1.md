---
title:  "Spring Framework 학습정리 - 1"
excerpt: "IoC 컨테이너 1부 : 스프링 IoC 컨테이너와 빈"
header:

categories:
  - Spring
tags:
  - Spring
  - Spring Framework
  - IoC 컨테이너
  - Bean
last_modified_at: 2020-07-27 20:20:00

---



# IoC 컨테이너와 빈

## 소개

### 강의 정보

- 스프링 프레임워크 핵심 기술
- 백기선

### 스프링 프레임워크 레퍼런스

- https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html

### 디자인 철학

- 모든 선택은 개발자의 몫이다. (스프링이 특정 영속화 기술을 강요하지 않는다.)
- 다양한 관점을 지향한다. (유연성)
- 하위 호환성을 지킨다.
- API를 신중하게 설계한다.
- 높은 수준의 코드를 지향한다.



## IoC 컨테이너 1부 : 스프링 IoC 컨테이너와 빈

### IoC (Inversion of Control)

- 의존관계 주입 (D.I) 이라고도 하며, 어떤 클래스 타입의 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법을 말한다.

- `BookServiece`가 사용 할 `BookRepository`객체를 직접 만들어서 사용하는 것이 아니고, 의존관계 주입을 받아서 사용하는 방법을 말한다.

  ```java
  // 생성자를 이용하여 의존관계를 주입
  @Service
  public class BookService {
      private BookRepository bookRepository;
    
      public BookService(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
      }
  }
  
  // 직접 객체를 생성하는 방식 (사용 X)
  @Service
  public class BookService {
  
      private BookRepository bookRepository = new BookRepository();
  }
  ```

### 스프링에서 제공하는 IoC 컨테이너를 사용하는 이유?

- 여러 D.I 방법과 노하우가 쌓여있는 프레임워크이기 때문이다.
- 오늘날에는 `@Service, @Repository, @Autowired` 등 `Anotaion`을 사용하여 `POJO` 객체를 `Bean`으로 등록한다.

### Spring IoC Container

- `BeanFactory` : 스프링 IoC 컨테이너의 가장 최상위 인터페이스이다. 다양한 컨테이너 구현체가 있지만 주로 사용하는 것은 `ApplicationContext`이다.
- `IoC` 기능을 제공하는 컨테이너
- 우리는 이 컨테이너 안에 있는 `Bean`들을 가져와서 사용한다.

### Bean

- 스프링 IoC 컨테이너가 관리하는 객체

- 스프링 IoC 컨테이너에 등록되는 빈들은 기본적으로 싱글톤 스코프 빈으로 등록된다.

- 장점 및 사용하는 이유

  ```
  - 의존성 주입 등의 관리를 위하여 사용 (의존성을 주입하기 위해서는 빈으로 등록되어야 한다.)
  - 스코프 : 싱글톤을 사용하여 관리하고 싶을 때도 IoC 컨테이너로 관리하면 편리하다.
    - 싱글톤 : BookService를 여러개 만들 필요없이 하나만 만들어 사용한다.
    - 프로포토타입 : 매번 다른 객체를 만들어 사용한다.
  - 라이프사이클 인터페이스를 지원해준다.
  ```

### 싱글톤 패턴의 장점

- 어플리케이션 전반에서 스프링 IoC 컨테이너로부터 `BookService, BookRepository` 등을 받아서 사용한다.
  - 그 인스턴스들은 항상 같은 객체이며, 메모리 및 성능최적화 면에서도 효율적이다.
  - `DB`와 연관되어있는 `Repository` 객체들은 생성 비용이 비싼데 싱글톤으로 관리하면 비용면에서도 효율적이다.
