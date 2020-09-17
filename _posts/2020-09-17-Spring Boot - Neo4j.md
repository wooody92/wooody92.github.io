---
title:  "Spring Boot - Neo4j"
excerpt: "스프링 Data : Neo4j 설정과 연결"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - Neo4j
  - SessionFactory
  - Neo4jRepository
last_modified_at: 2020-09-17. 15:20:00

---

<br>

# 스프링 부트 활용

## Spring Data 11부 : Neo4j

> Neo4j 설정방법과 사용법에 대해 알아보자.

### Neo4j 설정하기

- Neo4j는 노드간의 연관 관계를 영속화하는데 유리한 그래프 데이터베이스이다.

- 노드들과 연관관계를 표현하는데 있어서 풍부한 기능들을 제공하고 그런 부분에 있어서는 관계형 DB보다 조금 더 빠르다는 장점이 있는 그래프 DB이다.

  - 예시 - 친구의 친구의 친구를 찾기

- 사용을 위해 의존성을 추가한다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-neo4j</artifactId>
    <version>2.2.5.RELEASE</version>
  </dependency>
  ```

- 도커로 `MongoDB`를 실행한다.

  ```java
  // 7474 HTTP, 7687 볼트 프로토콜 
  docker run -p 7474:7474 -p 7687:7687 -d --name noe4j_boot neo4j
  ```

- Neo4j는 하위 호환성이 좋지않다. 포트 맵핑도 두 개 연결 해줘야 한다.

- 정상적으로 접속되면 아래 UI로 접속할 수 있다.

  ```
  http://localhost:7474/browser
  
  spring.data.neo4j.username=neo4j
  spring.data.neo4j.password=neo4j
  ```

- 기본적으로 `Neo4J`를 사용하는데 두 가지 방법이 있다.

  - `SessionFactory`
  - `extends Neo4jRepository`

 

### SessionFactory

- `@NodeEntity`로 엔티티를 생성하고 데이터를 입력해보자. 사용하는 `@Id`와 같은 애노테이션도 Neo4j의 패키지에서 가져와야 한다.

- `@Relationship(type = "has")`으로 관계를 정의 할 수 있다.

  ```java
  @NodeEntity
  @Getter @Setter
  public class Account {
  
      @Id @GeneratedValue
      private Long id;
  
      private String username;
  
      private String email;
  
      @Relationship(type = "has")
      private Set<Role> roles = new HashSet<>();
  }
  ```

  ```java
  @NodeEntity
  @Getter @Setter
  public class Role {
  
      @Id @GeneratedValue
      private Long id;
  
      private String name;
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      SessionFactory sessionFactory;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Account account = new Account();
          account.setEmail("henry@mail.com");
          account.setUsername("henry");
  
          Role role = new Role();
          role.setName("admin");
  
          account.getRoles().add(role);
  
          Session session = sessionFactory.openSession();
          session.save(account);
          sessionFactory.close();
      }
  }
  ```

  ![neo4j](https://user-images.githubusercontent.com/58318041/93299099-764b0200-f82f-11ea-86ce-0f7a8b5ab998.png)



### Neo4jRepository

- 같은 방식으로 `Neo4jRepository`를 이용하여 데이터를 입력 할 수 있다.

  ```java
  public interface AccountRepository extends Neo4jRepository<Account, Long> {
  }
  ```

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      AccountRepository accountRepository;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Account account = new Account();
          account.setEmail("henry@mail.com");
          account.setUsername("henry");
  
          Role role = new Role();
          role.setName("admin");
          account.getRoles().add(role);
  
          accountRepository.save(account);
      }
  }
  ```

  

## Spring Data 12부 : 정리

### 개요

- 자세한 내용은 공식문서를 참고하고 주로 사용하는 DB 중점으로 학습하도록 하자.

- [https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-sql](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-sql)

  

### SQL DB

- Spring JDBC가 있으면 JdbcTemplate을 빈으로 만들어줘서 쉽게 사용할 수 있다.
- Datasource 설정은 spring.datasource.* 방식으로 사용했다.
- embedded DB는 H2 위주로 사용하고 의존성만 추가해도 바로 사용가능하다.
- production DB에 연결하려면 보통 default 의존성으로 되어있는 HikariCP 사용한다.

### NoSQL

- 주로 template을 상속받아 사용하는 방법과 repository를 사용하는 방법이 있다.

