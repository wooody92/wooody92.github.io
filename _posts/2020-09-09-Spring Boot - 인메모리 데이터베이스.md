---
title:  "Spring Boot - In-memory DB"
excerpt: "스프링 Data : 인메모리 데이터베이스와 DataSource"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - 인메모리 데이터베이스
  - DataSource
last_modified_at: 2020-09-09. 03:30:00

---

<br>

# 스프링 부트 활용

## Spring Data 1부 : 소개

### SQL DB와 NoSQL

- ![DB](https://user-images.githubusercontent.com/58318041/92439678-3a3bef80-f1e6-11ea-8fc9-5092432ae34b.png)



## Spring Data 2부 : 인메모리 데이터베이스

> 스프링 부트가 지원하는 인메모리 데이터베이스에 대해 알아보자

### 인메모리 데이터베이스

- 스프링 부트가 지원하는 인메모리 데이터베이스로는 `H2`, `HSQL`, `Derby`가 있는데 콘솔환경에서 편하게 사용할 수 있기 때문에 `H2`사용을 권장한다.

- `H2`와 `Spring Jdbc` 사용을 위해 아래와 같이 의존성을 추가한다.

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
  </dependency>
  
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
  </dependency>
  ```

- `Spring Jdbc`가 클래스패스에 있으면 자동 설정이 필요한 빈을 설정 해준다.

  - `DataSource` -  `org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration`
  - `JdbcTemplate` - `org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration`

### DataSource

- 기본적으로 아무런 데이터 소스를 설정하지 않으면 인메모리 데이터소스로 설정해준다.

- `dataSource`로 접속할 `DB` 정보를 확인할 수 있다.

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      DataSource dataSource;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Connection connection = dataSource.getConnection();
          String url = connection.getMetaData().getURL();
          String userName = connection.getMetaData().getUserName();
          System.out.println("url : " + url);
          System.out.println("userName : " + userName);
      }
  }
  ```

  ```java
  // result
  
  url : jdbc:h2:mem:9b64dc69-545e-4b2d-bbcb-dbfeb3ec3384
  userName : SA
  ```

- 기본 연결 정보는 아래와 같고, `DataSourceProperties` 클래스에서 기본설정 되어있는 것을 확인할 수 있다.

  - `URL` : `"testdb"`
  - `username` : `"sa"`
  - `password` : `""`

  ```java
  // DataSourceProperties Class
  public String determineDatabaseName() {
  		if (this.generateUniqueName) {
  			if (this.uniqueName == null) {
  				this.uniqueName = UUID.randomUUID().toString();
  			}
  			return this.uniqueName;
  		}
  		if (StringUtils.hasLength(this.name)) {
  			return this.name;
  		}
  		if (this.embeddedDatabaseConnection != EmbeddedDatabaseConnection.NONE) {
  			return "testdb";
  		}
  		return null;
  	}
  ```

### H2 Console 사용하는 방법

- 방법 1 : `spring-boot-devtools` 의존성 추가

- 방법 2 : `spring.h2.console.enabled=true` 의존성 추가

- `/h2-console`로 접속

- 아래 코드와 같이 `JdbcTemplate`도 빈으로 만들어주기 때문에 사용할 수 있다.

- 스프링 Jdbc가 제공하는 `JdbcTemplate` 을 사용하면 더 간결하고 안전하게 `sql`을 사용할 수 있다.

  - `Try-catch` 같은 리소스 반납처리
  - 에러 계층구조를 잘 설계해놓아서 예외처리시에 좀 더 가독성이 높은 에러메세지 확인 가능

  ```java
  @Component
  public class SampleRunner implements ApplicationRunner {
  
      @Autowired
      DataSource dataSource;
  
      @Autowired
      JdbcTemplate jdbcTemplate;
  
      @Override
      public void run(ApplicationArguments args) throws Exception {
          try (Connection connection = dataSource.getConnection()) {
              Statement statement = connection.createStatement();
              String sql = "CREATE TABLE USER(ID INTEGER NOT NULL, name VARCHAR(255), PRIMARY KEY (id))";
              statement.executeUpdate(sql);
          }
          jdbcTemplate.execute("INSERT INTO USER VALUES (1, 'henry')");
      }
  }
  ```

