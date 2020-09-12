---
title:  "Spring Boot - ORM, Spring Data JPA"
excerpt: "스프링 Data : ORM, JPA, Spring Data JPA, DataJpaTest"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - ORM
  - JPA
  - Spring Data JPA
  - DataJpaTest
last_modified_at: 2020-09-12. 21:50:00

---

<br>

# 스프링 부트 활용

## Spring Data 5부 : ORM, JPA, Spring Data JPA 개요

> ORM, JPA, Spring Data JPA에 대해 알아보자.



### ORM (Object Relational Mapping)

- 객체와 관계형 DB 테이블을 맵핑 할 때 발생하는 개념적 불일치를 해결하는 프레임워크이다.

- `ORM`은 아래의 예시들과 같은 문제를 관리하고 해결해준다,

  ```
  1. 클래스는 타입이 다양하지만, 테이블에는 컬럼과 밸류값이 전부이다. 이를 어떻게 일치시킬 것인가
  2. 클래스 간의 상속구조를 어떻게 테이블에서 맵핑할 것인가
  3. identity 문제를 어떻게 관리할 것인가. 관계형 DB에서 id는 id, primary key, foreign key 등으로 관리 할 수 있다. 클래스 객체에서 id는 무엇을 기준으로 해야 하는가? hash code? equals method?
  4. 객체의 identity가 같다면 어느 엔티티가 같아야 같은 객체로 볼 수 있는가? id가 null인 경우에는 어떻게 처리할까?
  ```

- 위와 같이 발생할 수 있는 개념적 불일치를 ORM이 솔루션을 제시하여 해결해준다.

- `JPA`는 이런 `ORM` 솔루션 중에서 `Java` 표준을 정한 것이다.

- 대부분의 `Java` 표준이 `Hibernate` 기반이고, 대부분의 `JPA`의 기능도 `Hibernate`을 기반하여 만들어졌다.

- 하지만 `JPA`가 `Hibernate`의 모든 기능을 커버하진 못하지만, 약간의 설정으로 해결가능하여 사용에 지장은 없다.

- 그렇다면 `JPA`와 `Spring Data JPA`의 차이는 무엇일까? `Spring Data JPA`는 이러한 `JPA` 기능을 아주 쉽게 사용할 수 있도록 스프링 데이터로 추상화 해놓은 것이다.

- 추상화 : `Spring Data JPA` > `JPA` > `Hibernate` > `Datasource (JDBC)`

  - 결국 여기서 추상화란 `Spring Data JDBC`의 모든 기능을 사용하면서, 부가적으로 `Spring Data JPA`를 위한 기능들을 추가한 것이다.

- 의존성을 살펴보면 `spring-boot-starter-data-jpa` 하위에 `hibernate-core`가 `JPA`의 구현체이다.

<br>

## Spring Data 6부 : Spring Data JPA 연동

### Spring Data JPA

- `Repository` 빈 자동 생성

- 쿼리 메서드 자동 구현

- 원래 사용을 위해서는 `@EnableJpaRepositories`를 붙여 설정을 해야하지만, 스프링 부트가 자동으로 설정해준다.

- 아래 의존성을 추가하면 스프링 부트가 모든 자동설정을 지원해준다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <version>2.2.6.RELEASE</version>
  </dependency>
  ```

- 인메모리 데이터베이스가 아닌 데이터베이스를 사용하려면 `application.properties`에서 연결 설정을 해줘야 한다. 설정하지 않으면 인메모리로 띄운다.

  ```java
  spring.datasource.url=jdbc:mysql://localhost:3306/mydb
  spring.datasource.username=henry
  spring.datasource.password=1234
  ```

  

### @DataJpaTest (슬라이스 테스트)

- `Repository`와 관련된 빈들만 등록을 하여 테스트한다.

- 슬라이스 테스트 환경에는 인메모리 데이터베이스(`H2`)가 필요하다.

  ```xml
  // test 환경에서만 동작한다.
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>test</scope>
  </dependency>
  ```

- 애플리케이션과 테스트가 다른 `DB`를 사용할 수 있다. (애플리케이션 : `MySQL`, 테스트 : `H2`)

  ```java
  @RunWith(SpringRunner.class)
  @DataJpaTest
  public class AccountRepositoryTest {
  
      @Autowired
      DataSource dataSource;
  
      @Autowired
      JdbcTemplate jdbcTemplate;
  
      @Autowired
      AccountRepository accountRepository;
  
      @Test
      public void di() throws SQLException {
          try(Connection connection = dataSource.getConnection()) {
              DatabaseMetaData metaData = dataSource.getConnection().getMetaData();
              System.out.println(metaData.getURL());
              System.out.println(metaData.getDriverName());
              System.out.println(metaData.getUserName());
          }
      }
  
      @Test
      public void account() {
          Account account = new Account();
          account.setUsername("henry");
          account.setPassword("pass");
  
          Account newAccount = accountRepository.save(account);
          assertThat(newAccount).isNotNull();
  
          Optional<Account> existingAccount = accountRepository.findByUsername(newAccount.getUsername());
          assertThat(existingAccount).isNotEmpty();
  
          Optional<Account> nonExistingAccount = accountRepository.findByUsername("wooody92");
          assertThat(nonExistingAccount).isEmpty();
      }
  }
  ```

  ```java
  // result
  jdbc:h2:mem:1f515463-6796-405b-9ef6-e81f8fad4096
  H2 JDBC Driver
  SA
  ```

- `@DataJpaTest` 대신 `@SpringBootTest`(통합 테스트)를 사용한다면 DB를 구분하는 것이 안전하다.

  - 그렇지 않으면 테스트 환경에서의 DB변경이 애플리케이션 DB에 영향을 준다.

- 통합 테스트를 사용하여 DB 설정을 나눈다면, `@SpringBootTest(properties = "spring.datasource.url=")`을 사용하여 오버라이드 할 수 있다.

- 하지만 슬라이스 테스트가 빠르고 간단하고 안전하므로 권장한다.

- `Repository`에서 아래와 같이 다양한 방식으로 메서드를 생성할 수 있다.

  ```java
  // 자동생성 (규칙에 맞추어 이름을 지어야 한다.)
  Optional<Account> findByUsername(String userName);
  
  // jpql
  @Query("jpql 명령어")
  Optional<Account> findByUsername(String userName);
  
  // native query
  @Query(nativeQuery = true, value = "SELECT * FROM account WHERE username = :userName")
  Optional<Account> findByUsername(String userName);
  ```

  