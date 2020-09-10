---
title:  "Spring Boot - DBCP와 MySQL 설정"
excerpt: "스프링 Data : DBCP, HikariCP, MySQL, PostgreSQL"
header:

categories:
  - Spring Boot
tags:
  - Spring
  - Spring Boot
  - Spring Data
  - DBCP
  - HikariCP
last_modified_at: 2020-09-10. 20:30:00

---

<br>

# 스프링 부트 활용

## Spring Data 3부 : MySQL

> 스프링 부트가 지원하는 `DBCP`와 설정법에 대해 알아보자.

### DBCP

- `DBCP(Data Base Connection Pool)`
  - 애플리케이션과 DB를 연결하는 과정은 간단한 작업이 아니다.
  - 그렇기 때문에 연결전에 미리 커넥션을 몇개 만들어 놓는다. (`Connection Pool`)
  - `DPCP`에서 이러한 커넥션을 얼마나 만들어놓고, 최소 몇개의 커넥션을 유지하고 DB 커넥션 객체를 애플리케이션에 얼마의 시간동안 전달하지 못하면 에러를 발생시키고 하는 설정들을 관리한다.
  - 이와 같이 `DBCP`는 애플리케이션 성능아 아주 핵심적인 역할을 하고 문제가 발생하면 디버깅도 까다롭기때문에 선택과 학습을 잘하고 사용 및 관리를 해야한다.
- 스프링 부트에서 지원하는 `DBCP`는 `HikariCP`, `Tomcat CP`, `Commons DBCP2`가 있고, 기본적으로 `HikariCP`를 사용한다.

### HikariCP

- [https://github.com/brettwooldridge/HikariCP](https://github.com/brettwooldridge/HikariCP)

- `autoCommit` : `default - true`

  - `SQL`을 실행하고 커밋이라고 명시를 하지 않아도, 커넥션을 리턴할 때 자동으로 커밋한다. (변경사항 저장)

- `connectionTimeout` : `default - 30000`

  - `DBCP`에서 애플리케이션에 커넥션 객체를 전달하는데 얼마나 기다리고 예외를 발생시킬 것인지를 말한다.
  - 당연히 시간이 짧을 수록 에러가 발생할 확률이 높아진다.

- `maximumPoolSize` : `default - 10`

  - 커넥션 객체를 몇 개를 유지할 것인지 기본값은 10개이다.
  - 많다고 좋은 것도 아니다.
  - 애플리케이션이 동시에 한번에 일을 할 수 있는 실행할 수 있는 cpu 코어 겟수와 같다.
  - 초과된 커넥션(쓰레드)들은 대기한다.

- `properties`로 `DBCP`를 설정할 수 있다.

  ```java
  // hikari
  spring.datasource.hikari.connection-timeout=10000
  spring.datasource.hikari.maximum-pool-size=6
    
  // tomcat, dbcp2도 동일하다.
  spring.datasource.tomcat.*
  spring.datasource.dbcp2.*
  ```

### MySQL

- `MySQL`에 접속할 수 있는 커넥터 의존성을 추가한다. (`dataSource` 구현체)

  ```xml
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
  </dependency>
  ```

- `Docker container`로 `MySQL`을 실행하고 `Database`를 생성한다.

  ```java
  - docker run -p 3306:3306 --name mysql_boot -e MYSQL_ROOT_PASSWORD=1 -e
  MYSQL_DATABASE=mydb -e MYSQL_USER=henry -e
  MYSQL_PASSWORD=1234 -d mysql
  - docker exec -i -t mysql_boot bash
  - mysql -u root -p
  ```

- `properties`에 생성한 `DB`로 접속한다.

  ```java
  spring.datasource.url=jdbc:mysql://localhost:3306/mydb
  spring.datasource.username=henry
  spring.datasource.password=1234
  ```

<br>

## Spring Data 4부 : PostgreSQL

### PostgreSQL

- 의존성 추가

  ```xml
  <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <version>42.2.12</version>
  </dependency>
  ```

- `Docker container`로 `PostgreSQL`을 실행하고 `Database`를 생성한다.

  ```java
  - docker run -p 5432:5432 -e POSTGRES_PASSWORD=1234 -e POSTGRES_USER=henry -e POSTGRES_DB=mydb --name postgres_boot -d postgres
  - docker exec -i -t postgres_boot bash
  - su - postgres
  - psql springboot
  - \list (데이터베이스 조회)
  - \dt (테이블 조회)
  ```

- `properties`에 생성한 `DB`로 접속한다.

  ```java
  spring.datasource.url=jdbc:postgresql://localhost:3306/mydb
  spring.datasource.username=henry
  spring.datasource.password=1234
  ```

  