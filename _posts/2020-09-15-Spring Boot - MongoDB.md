---
title:  "Spring Boot - MongoDB"
excerpt: "스프링 Data : MongoDB 설정과 연결"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - MongoDB
  - MongoTemplate
  - MongoRepository
  - DataMongoTest
last_modified_at: 2020-09-15. 15:20:00

---

<br>

# 스프링 부트 활용

## Spring Data 10부 : MongoDB

> MongoDB 설정방법과 사용법에 대해 알아보자.

### MongoDB 설정하기

- `MongoDB`는 JSON 기반의 도큐먼트 데이터베이스이기 때문에 스키마가 없는 것이 특징이다.

- 사용을 위해 의존성을 추가한다.

  ```xml
  <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-mongodb</artifactId>
      <version>3.0.1.RELEASE</version>
  </dependency>
  ```

- 도커로 `MongoDB`를 실행한다. 로컬의 27017 포트로 컨테너의 27017 포트를 연결한다.

  ```
  docker run -p 27017:27017 --name mongo_boot -d mongo
  docker exec -i -t mongo_boot bash
  mongo
  ```

- 기본적으로 `MongoDB`를 사용하는데 두 가지 방법이 있다.

  - `MongoTemplate`
  - `extends MongoRepository`



### MongoTemplate

- 엔티티를 만들어 보자. `collection`이 SQL로 치면 테이블 이름 정도라고 생각하면 된다.

  ```java
  @Document(collection = "accounts")
  @Getter @Setter
  public class Account {
  
      @Id
      private String id;
  
      private String username;
  
      private String email;
  }
  ```

- 기본적으로 `MongoDB`를 스프링 데이터와 사용하기 위해서는 여러 설정을 해줘야하지만, 스프링 부트에서 `MongoTemplate`, `MongoRepository`와 같이 기능을 지원해주기 때문에 특별한 설정없이 의존성 주입만으로 사용 할 수 있다.

  ```java
  @SpringBootApplication
  public class Application {
  
      @Autowired
      MongoTemplate mongoTemplate;
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.setWebApplicationType(WebApplicationType.NONE);
          app.run(args);
      }
  
      @Bean
      public ApplicationRunner applicationRunner() {
          return args -> {
              Account account = new Account();
              account.setEmail("aaa@bbb");
              account.setUsername("aaa");
              mongoTemplate.insert(account);
              System.out.println("finished");
  
          };
      }
  }
  ```

  ![mongo1](https://user-images.githubusercontent.com/58318041/93174865-b47dee00-f769-11ea-83cf-c7e6e1f97190.png)



### MongoRepository

- `MongoRepository`를 사용하여 데이터를 입력해도 정상적으로 동작하는 것을 볼 수 있다.

  ```java
  public interface AccountRepository extends MongoRepository<Account, String> {
  }
  ```

  ```java
  @SpringBootApplication
  public class Application {
  
      @Autowired
      AccountRepository accountRepository;
  
      public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.setWebApplicationType(WebApplicationType.NONE);
          app.run(args);
      }
  
      @Bean
      public ApplicationRunner applicationRunner() {
          return args -> {
              Account account = new Account();
              account.setEmail("whiteship@email.com");
              account.setUsername("whiteship");
              accountRepository.insert(account);
          };
      }
  }
  ```

  ![mongo2](https://user-images.githubusercontent.com/58318041/93175006-f3ac3f00-f769-11ea-97e0-e041420204d4.png)



### 테스트(@DataMongoTest)

- 테스트를 진행 할 때 운영용 `MongoDB`에 테스트를 진행하는 것은 문제가 있다. 그래서 테스트를 위한 내장형 `MongoDB`가 있다. 아래와 같이 의존성을 추가하여 사용한다.

  ```xml
  <dependency>
      <groupId>de.flapdoodle.embed</groupId>
      <artifactId>de.flapdoodle.embed.mongo</artifactId>
      <version>2.2.0</version>
      <scope>test</scope>
  </dependency>
  ```

- `@DataMongoTest` : `MongoRepository` 관련된 빈들만 등록되어 슬라이스 테스트 할 수 있다.

  ```java
  @RunWith(SpringRunner.class)
  @DataMongoTest
  public class AccountRepositoryTest {
  
      @Autowired
      AccountRepository accountRepository;
  
      @Test
      public void findByEmail() {
          Account account = new Account();
          account.setUsername("henry");
          account.setEmail("henry@mail.com");
  
          accountRepository.save(account);
  
          Optional<Account> byId = accountRepository.findById(account.getId());
          assertThat(byId).isNotEmpty();
  
          Optional<Account> byEmail = accountRepository.findByEmail(account.getEmail());
          assertThat(byEmail).isNotEmpty();
          assertThat(byEmail.get().getUsername()).isEqualTo("henry");
      }
  }
  ```

  

